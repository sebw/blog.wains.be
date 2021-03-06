---
date: 2020-10-30
title: "Migrating from false positives of Synology Surveillance Station to Zoneminder and very accurate motion detection and notification"
---

## Motion without object detection is worthless

I replaced my old Synology DS213j with a Fedora box. I have talked about this in a previous post.

The one thing that held me for a while was Synology's take on home security, called Surveillance Station.

It was holding me from moving to a fully open source alternative, but admittedly Surveillance Station never worked quite well with my outdoor cameras (Eminent EM6360).

I was getting one million false positives a day and just decided to turn off notifications and just record constantly, and check the recording in case of break in.

This is not ideal and I really wanted to get notified if something was actually happening.

I started looking for open source alternatives.

## Zoneminder?

I first played with Zoneminder probably more than 10 years ago and decided to give it another shot.

I installed Zoneminder in its purest form and by the evening it started producing as many false positives as Surveillance Station (rain and infrared night vision is considered as motion). I got 400 notifications in an hour.

During my research in order to reduce the false positives issues, I discovered machine learning and object detection was a possibility through the use of [zmeventnotification](https://zmeventnotification.readthedocs.io/en/latest/).

I was initially confused by the name because it sounded just like a notification system. It's actually more than that.

Zoneminder will detect events as usual (many of them being just false positives) and zmeventnotification will process the event with a `zm_events.py` script and some opencv magic.

A notification is created only if an object (a person, a car, a boat, etc.) is detected. The neat thing is that you can use any script to notify you (that's what I do).

Let me tell you, it took me quite some time to figure out how to get it running but when you do it works amazingly well.

## Zoneminder + object detection = awesome

Look at that:

![](https://blog.wains.be/images/zoneminder-gotify.png)

Someone amazing called "dlandon" created a container that ships both Zoneminder and zmeventnotification.

You can get push notifications to your mobile device if you use the zmninja (paid) mobile application.

I purchased the app but decided not to use the built-in notification. I decided to use Gotify instead (which is my default choice for any kind of notifications, it is pretty awesome software).

Even if you don't need the notifications, the Android app is pretty well worth the 5 or so euros.

## Neat. Do I need fancy hardware for object detection?

I run Zoneminder on a 4 CPU and 6 GB VM, with 5 cameras. It performs globally well enough for a domestic usage.

I get notified within 30 seconds of the actual event, which is good enough for me.

The host has a reasonably inexpensive AMD Ryzen 5 3400G.

## How to get the whole thing running?

On a Fedora box install `docker-ce` or `moby-engine`:

```bash
dnf install moby-engine
```

Make sure to add the docker interface to the firewalld trusted zone (I didn't and the container couldn't resolve):

```bash
firewall-cmd --permanent --zone=trusted --add-interface=docker0
firewall-cmd --reload
```

Create some folders for your container data:

```bash
mkdir -p /opt/docker/zm/{config,data}
```

Install the container:

```bash
docker run -d --name="zm" \
--net="bridge" \
--privileged="true" \
-p 8080:80/tcp \
-p 9000:9000/tcp \
-e TZ="Europe/Brussels" \
-e SHMEM="50%" \
-e PUID="1000" \
-e PGID="1000" \
-e INSTALL_HOOK="1" \
-e INSTALL_FACE="1" \
-e INSTALL_TINY_YOLO="1" \
-e INSTALL_YOLO="0" \
-v "/opt/docker/zm/config":"/config":rw \
-v "/opt/docker/zm/data":"/var/cache/zoneminder":rw \
dlandon/zoneminder
```

## Edit Jan 2021

In the latest image update, the container didn't seem to take the `SHMEM` variable into account, resulting in a `/dev/shm/` of 64 MB, the default.

When that happens, Zoneminder processes will fail miserably, returning errors 124 or 255.

My workaround has been to create a tmpfs on the host and map the volume.

```
mount -t tmpfs -o size=3G tmpfs /mnt/dockershm
```

If that happens, add the following to the `docker run` command above:

```
-v "/mnt/dockershm":"/dev/shm"
```

## End of edit

The container will compile a whole bunch of things, it took about 35 minutes on my small VM.

When Zoneminder is up and running, add your cameras (look it up).

In order to enjoy object detection, you will need to configure some stuff for zmeventnotification.

On the docker host configure those:

`/opt/docker/zm/config/secrets.ini`:

```ini
[secrets]
ZMES_PICTURE_URL=http://zm.example.lan:8080/zm/index.php?view=image&eid=EVENTID&fid=objdetect&width=600
ZM_USER=admin
ZM_PASSWORD=changethisifyouenabledauthentication
ES_ADMIN_INTERFACE_PASSWORD=your_admin_interface_password
ZM_PORTAL=http://zm.example.lan:8080/zm
ZM_API_PORTAL=http://zm.example.lan:8080/zm/api
ES_CERT_FILE = /etc/apache2/ssl/zoneminder.crt
ES_KEY_FILE = /etc/apache2/ssl/zoneminder.key
ML_USER=your_mlapi_user
ML_PASSWORD=your_mlapi_password
PLATEREC_ALPR_KEY=your_plate_recognizer_api_key
OPENALPR_ALPR_KEY=your_openalpr_api_key
ESCONTROL_INTERFACE_PASSWORD=yourescontrolpassword
MQTT_USERNAME=your_mqtt_username
MQTT_PASSWORD=your_mqtt_password
PUSHOVER_APP_TOKEN=your_pushover_app_token
PUSHOVER_USER_KEY=your_pushover_user_key
```

Most options are left untouched. In my case I changed `ZMES_PICTURE_URL`, `ZM_PORTAL` and `ZM_API_PORTAL`.

Please note you won't be able to test the ZMES URL as long as the event server is not properly functioning.

If you want to enable authentication (I didn't, my Zoneminder is not exposed to the internet), change the `ZM_USER` and `ZM_PASSWORD` variables.

I was thrown out by the other variables but just left those untouched and it was fine.

`/opt/docker/zm/config/zmeventnotification.ini`:

```ini
[general]
secrets = /etc/zm/secrets.ini
base_data_path=/var/lib/zmeventnotification
use_escontrol_interface=no
escontrol_interface_file=/var/lib/zmeventnotification/misc/escontrol_interface.dat
escontrol_interface_password=!ESCONTROL_INTERFACE_PASSWORD
restart_interval = 0
[network]
port = 9000
[auth]
enable = yes
timeout = 20
[push]
use_api_push = yes
api_push_script=/config/hook/gotify.sh
[fcm]
enable = no
use_fcmv1 = yes
replace_push_messages = no
token_file = {{base_data_path}}/push/tokens.txt
date_format = %I:%M %p, %d-%b
[mqtt]
enable = no
server = 127.0.0.1
retain = no
[ssl]
enable = yes
cert = !ES_CERT_FILE
key = !ES_KEY_FILE
[customize]
console_logs = no
es_debug_level = 2
event_check_interval = 5
monitor_reload_interval = 300
read_alarm_cause = yes
tag_alarm_event_id = yes
use_custom_notification_sound = no
include_picture = yes
send_event_end_notification = no
picture_url = !ZMES_PICTURE_URL
picture_portal_username=!ZM_USER
picture_portal_password=!ZM_PASSWORD
use_hooks = yes
[hook]
event_start_hook = '{{base_data_path}}/bin/zm_event_start.sh'
event_end_hook = '{{base_data_path}}/bin/zm_event_end.sh'
event_start_notify_on_hook_success = all
event_start_notify_on_hook_fail = none
event_end_notify_on_hook_success = fcm,web,api
event_end_notify_on_hook_fail = none
event_end_notify_if_start_success = yes
use_hook_description = yes
keep_frame_match_type = yes
hook_pass_image_path = yes
```

In this file I edited `api_push_script` which calls `/config/hook/gotify.sh` for Gotify notifications upon events.

Create `/opt/docker/zm/config/hook/gotify.sh`:

```bash
#!/bin/bash

EVENT_ID=`echo ${6} | awk -F'/' '{ print $8 }'`

curl --request POST \
  --url 'https://gotify.example.org/message?token=XXXXXXXXXXXXXX' \
  --header 'content-type: application/json' \
  --data "{
    \"title\": \"Zoneminder - $3\",
    \"message\": \"$4\n\n ![Connect to VPN to see image.](http://zm.example.lan:8080/zm/index.php?view=image&eid=${EVENT_ID}&fid=objdetect&popup=1)\",
    \"priority\": 5,
      \"extras\": {
    \"client::display\": {
      \"contentType\": \"text/markdown\"
    }
  }
}"
```

My Zoneminder is not exposed to the internet so I must connect to the VPN if I want to see the image in the notification. Otherwise it just shows a message "Connect to VPN to see image".

Lastly edit the object config file:

`/opt/docker/zm/config/hook/objectconfig.ini`:

```ini
[general]
version=1.1
secrets = /etc/zm/secrets.ini
cpu_max_processes=3
tpu_max_processes=1
gpu_max_processes=1
cpu_max_lock_wait=120
tpu_max_lock_wait=120
gpu_max_lock_wait=120
base_data_path=/var/lib/zmeventnotification
pyzm_overrides={'log_level_debug':2}
base_zm_conf_path=/etc/zm
portal=!ZM_PORTAL
user=!ZM_USER
password=!ZM_PASSWORD
api_portal=!ZM_API_PORTAL
allow_self_signed=yes
match_past_detections=yes
past_det_max_diff_area=5%
detection_sequence=object,face
detection_mode=all
frame_id=bestmatch
resize=1200
delete_after_analyze=yes
write_debug_image=no
write_image_to_zm=yes
show_percent=yes
poly_color=(255,255,255)
poly_thickness=2
[remote]
[object]
object_detection_pattern=person
object_min_confidence=0.3
object_framework=opencv
object_processor=cpu # or gpu
object_config={{base_data_path}}/models/yolov3/yolov3.cfg
object_weights={{base_data_path}}/models/yolov3/yolov3.weights
object_labels={{base_data_path}}/models/yolov3/coco.names
[hog]
stride=(4,4)
padding=(8,8)
scale=1.05
mean_shift=-1
[face]
face_detection_framework=dlib
face_recognition_framework=dlib
known_images_path={{base_data_path}}/known_faces
save_unknown_faces=yes
save_unknown_faces_leeway_pixels=50
unknown_images_path={{base_data_path}}/unknown_faces
face_num_jitters=1
face_model=hog
face_upsample_times=1
face_recog_dist_threshold=0.6
face_train_model=hog
[alpr]
alpr_use_after_detection_only=yes
alpr_service=plate_recognizer
alpr_api_type=cloud
alpr_key=!PLATEREC_ALPR_KEY
platerec_stats=no
platerec_min_dscore=0.1
platerec_min_score=0.2
[animation]
create_animation=no
animation_types='gif'
fast_gif=no
animation_width=640
animation_retry_sleep=15
animation_max_tries=3
[monitor-999]
match_past_detections=no
wait=5
object_detection_pattern=(person|car|motorbike|bus|truck|boat)
my_driveway=306,356 1003,341 1074,683 154,715
my_driveway_zone_detection_pattern=(person)
some_other_area=0,0,200,300,700,900
resize=no
detection_sequence=object,alpr
```

I think I only changed `object_detection_pattern` in there. You can have custom patterns (detecting cars and people on the frontyard camera and only people on the backyard). I assume that reducing the number of objects reduces the processing (?).

What was missing are 3 files related to `yolov3`.

I tried object detection script manually from inside the container:

```bash
docker exec -it zm sh
sudo -u www-data /var/lib/zmeventnotification/bin/zm_detect.py --config /etc/zm/objectconfig.ini  --eventid 1 --monitorid 1 --debug
```

Where the monitor ID is 1 and I parse the first event ID 1.

This was throwing a bunch of errors all related to missing yolo files.

On the Docker host create this folder:

```bash
mkdir /opt/docker/zm/config/hook/models/yolov3
```

Download the missing files inside the newly created folder:

```bash
cd /opt/docker/zm/config/hook/models/yolov3
wget https://raw.githubusercontent.com/pjreddie/darknet/master/data/coco.names
wget https://raw.githubusercontent.com/pjreddie/darknet/master/cfg/yolov3.cfg
wget https://pjreddie.com/media/files/yolov3.weights
```

When you are done with the configurations files and the missing files, enable the 3rd party notification in the web UI and restart Zoneminder:

![](https://blog.wains.be/images/zoneminder-notif.png)

At this point, object detection must work. Congrats!

You can verify by running the object detection manually again (still inside the container) and it should not give errors:

```bash
docker exec -it zm sh
sudo -u www-data /var/lib/zmeventnotification/bin/zm_detect.py --config /etc/zm/objectconfig.ini  --eventid 1 --monitorid 1 --debug
```

While you are at it, you can also train the system for know persons.

Drop a couple of pictures of the persons in this kind of structure:

```bash
/opt/docker/zm/config/hook/known_faces/Sebastien/1.jpg
/opt/docker/zm/config/hook/known_faces/Sebastien/2.jpg
/opt/docker/zm/config/hook/known_faces/Sebastien/whatever.jpg
/opt/docker/zm/config/hook/known_faces/Postman/my_postman_is_cool.jpg
/opt/docker/zm/config/hook/known_faces/Bubbles/bubs.jpg
/opt/docker/zm/config/hook/known_faces/Ricky/rick.jpg
/opt/docker/zm/config/hook/known_faces/Julian/julian.jpg
```

I put 4 very small jpeg pictures of myself in there and face detection has been working well. Documentation suggests to avoid large images because it slows things down.

Run the training script from inside the container:

```bash
sudo -u www-data /var/lib/zmeventnotification/bin/zm_train_faces.py
```

This should output something like this:

```
Oct 30 2020 15:03:58.339439 [INF] Using simple log output (default)
Oct 30 2020 15:03:58.339489 [DBG 1] Face Recognition library load time took: 0.001 milliseconds
Oct 30 2020 15:03:58.339555 [DBG 1] Sebastien is a directory. Processing all images inside it
Oct 30 2020 15:03:58.339577 [DBG 1] loading face from  Sebastien/2.jpg
Oct 30 2020 15:03:58.401641 [DBG 1] loading face from  Sebastien/3.jpg
Oct 30 2020 15:03:58.466575 [DBG 1] loading face from  Sebastien/4.jpg
Oct 30 2020 15:03:58.527484 [DBG 1] loading face from  Sebastien/index.jpg
Oct 30 2020 15:03:59.259582 [DBG 2] Using n_neighbors to be: 3
Oct 30 2020 15:03:59.259680 [DBG 1] Training model ...
Oct 30 2020 15:03:59.261252 [DBG 1] wrote encoding file: /var/lib/zmeventnotification/known_faces/faces.dat
Oct 30 2020 15:03:59.261302 [DBG 1] Face Recognition training took: 921.791 milliseconds
```

Now your notification could mention the name of the person if the processed image is good enough (not blurry, not wearing hat or mask):

![](https://blog.wains.be/images/zoneminder-face-detection.png)

You will notice I'm 100% Sebastien but not fully human :D

## Conclusion

Thanks to a simple container and a few configuration changes, you can get a powerful object detection system.

I haven't received a false positive since I started using zmeventnotification.

Detection is very accurate and not resource intensive, my Zoneminder runs in a VM with 2 CPU and 4 GB or RAM (for 4 cameras).

I probably missed the part of the documentation regarding the missing yolo files, so I somewhat reverse engineered the whole process to discover what was missing. I should learn to RTFM :-)