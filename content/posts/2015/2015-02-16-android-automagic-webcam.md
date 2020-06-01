---
date: 2015-02-16
title: "Android Automagic- enable or disable motion detection on Dlink webcams"
---
Date: 2015-02-16
tags: Android, Automagic

This has been tested on DCS-930L and DCS-5020L

    <?xml version='1.0' encoding='UTF-8' standalone='yes' ?>
    <data version="1.25.0">
      <trigger type="wifi_connected">
        <useDefaultName>true</useDefaultName>
        <name>WiFi Connected: SSID</name>
        <enabled>true</enabled>
        <all>false</all>
        <ssidList>SSID</ssidList>
      </trigger>
      <trigger type="wifi_disconnected">
        <useDefaultName>true</useDefaultName>
        <name>WiFi Disconnected: SSID</name>
        <enabled>true</enabled>
        <all>false</all>
        <ssidList>SSID</ssidList>
      </trigger>
      <condition type="active_network_type">
        <useDefaultName>true</useDefaultName>
        <name>Active Network Type: Mobile</name>
        <none>false</none>
        <mobile>true</mobile>
        <wifi>false</wifi>
        <wimax>false</wimax>
        <bluetooth>false</bluetooth>
        <ethernet>false</ethernet>
      </condition>
      <action type="http_request">
        <useDefaultName>true</useDefaultName>
        <name>HTTP Request: POST https://webcam.public.url/setSystemMotion application/x-www-form-urlencoded ReplySuccessPage=motion.htm,ReplyErrorPage=motion.htm,MotionDetectionEnable=0,MotionDetectionScheduleDay=30,ConfigSystemMotion=Save store in motion</name>
        <url>https://webcam.public.url/setSystemMotion</url>
        <verifyCertificates>true</verifyCertificates>
        <basicAuthentication>true</basicAuthentication>
        <username>admin</username>
        <httpMethod>POST</httpMethod>
        <httpContentType>X_WWW_FORM_URLENCODED</httpContentType>
        <contentType>text/plain</contentType>
        <generalTextData></generalTextData>
        <formFieldList>ReplySuccessPage=motion.htm,ReplyErrorPage=motion.htm,MotionDetectionEnable=0,MotionDetectionScheduleDay=30,ConfigSystemMotion=Save</formFieldList>
        <timeout>60000</timeout>
        <storeInVariable>true</storeInVariable>
        <variable>motion</variable>
        <path>/storage/emulated/0/Download/file.bin</path>
      </action>
      <action type="http_request">
        <useDefaultName>true</useDefaultName>
        <name>HTTP Request: POST https://webcam.public.url/setSystemMotion application/x-www-form-urlencoded ReplySuccessPage=motion.htm,ReplyErrorPage=motion.htm,MotionDetectionEnable=1,MotionDetectionScheduleDay=30,MotionDetectionScheduleMode=0,MotionDetectionSensitivity=50,ConfigSystemMotion=Save store in response</name>
        <url>https://webcam.public.url/setSystemMotion</url>
        <verifyCertificates>true</verifyCertificates>
        <basicAuthentication>true</basicAuthentication>
        <username>admin</username>
        <httpMethod>POST</httpMethod>
        <httpContentType>X_WWW_FORM_URLENCODED</httpContentType>
        <contentType>text/plain</contentType>
        <generalTextData></generalTextData>
        <formFieldList>ReplySuccessPage=motion.htm,ReplyErrorPage=motion.htm,MotionDetectionEnable=1,MotionDetectionScheduleDay=30,MotionDetectionScheduleMode=0,MotionDetectionSensitivity=50,ConfigSystemMotion=Save</formFieldList>
        <timeout>60000</timeout>
        <storeInVariable>true</storeInVariable>
        <variable>response</variable>
        <path>/storage/emulated/0/Download/file.bin</path>
      </action>
      <action type="notification_status_bar">
        <useDefaultName>true</useDefaultName>
        <name>Notification on Statusbar: Webcam disabled House ID 2</name>
        <notificationIcon>HOUSE</notificationIcon>
        <title>Webcam disabled</title>
        <message>Motion disabled</message>
        <sound>false</sound>
        <vibrate>false</vibrate>
        <flashLED>false</flashLED>
        <flashLEDColor>#ff00ff00</flashLEDColor>
        <flashLEDOn>500</flashLEDOn>
        <flashLEDOff>500</flashLEDOff>
        <flagLocalOnly>false</flagLocalOnly>
        <flagOngoing>false</flagOngoing>
        <flagNoClear>false</flagNoClear>
        <notificationIDEnabled>true</notificationIDEnabled>
        <notificationID>2</notificationID>
        <priority>DEFAULT</priority>
        <visibility>PRIVATE</visibility>
        <messageBigEnabled>false</messageBigEnabled>
        <messageBig></messageBig>
        <largeIconEnabled>false</largeIconEnabled>
        <largeIcon></largeIcon>
      </action>
      <action type="notification_status_bar">
        <useDefaultName>true</useDefaultName>
        <name>Notification on Statusbar: Webcam enabled House ID 3</name>
        <notificationIcon>HOUSE</notificationIcon>
        <title>Webcam enabled</title>
        <message>Motion enabled</message>
        <sound>false</sound>
        <vibrate>false</vibrate>
        <flashLED>false</flashLED>
        <flashLEDColor>#ff00ff00</flashLEDColor>
        <flashLEDOn>500</flashLEDOn>
        <flashLEDOff>500</flashLEDOff>
        <flagLocalOnly>false</flagLocalOnly>
        <flagOngoing>false</flagOngoing>
        <flagNoClear>false</flagNoClear>
        <notificationIDEnabled>true</notificationIDEnabled>
        <notificationID>3</notificationID>
        <priority>DEFAULT</priority>
        <visibility>PRIVATE</visibility>
        <messageBigEnabled>false</messageBigEnabled>
        <messageBig></messageBig>
        <largeIconEnabled>false</largeIconEnabled>
        <largeIcon></largeIcon>
      </action>
      <action type="remove_notification_status_bar">
        <useDefaultName>true</useDefaultName>
        <name>Remove Notification on Statusbar: 2 (Automagic)</name>
        <automagicNotifications>true</automagicNotifications>
        <all>false</all>
        <notificationID>2</notificationID>
        <overall>true</overall>
        <packageName></packageName>
        <allOfApp>true</allOfApp>
        <filterNotificationID></filterNotificationID>
      </action>
      <action type="remove_notification_status_bar">
        <useDefaultName>true</useDefaultName>
        <name>Remove Notification on Statusbar: 3 (Automagic)</name>
        <automagicNotifications>true</automagicNotifications>
        <all>false</all>
        <notificationID>3</notificationID>
        <overall>true</overall>
        <packageName></packageName>
        <allOfApp>true</allOfApp>
        <filterNotificationID></filterNotificationID>
      </action>
      <action type="sleep">
        <useDefaultName>true</useDefaultName>
        <name>Sleep: 15s (allow device sleep)</name>
        <duration>15s</duration>
        <keepDeviceAwake>false</keepDeviceAwake>
      </action>
      <flow type="flow">
        <name>Webcam disable motion</name>
        <group>Webcam</group>
        <enabled>true</enabled>
        <lastExecutionStartTime>1417460412281</lastExecutionStartTime>
        <lastExecutionEndTime>1417460413698</lastExecutionEndTime>
        <executionPolicy>PARALLEL</executionPolicy>
        <triggercontainer id="t1" x="-70.0" y="87.5">
          <trigger>WiFi Connected: SSID</trigger>
        </triggercontainer>
        <actioncontainer id="t2" x="-70.0" y="262.5">HTTP Request: POST https://webcam.public.url/setSystemMotion application/x-www-form-urlencoded ReplySuccessPage=motion.htm,ReplyErrorPage=motion.htm,MotionDetectionEnable=0,MotionDetectionScheduleDay=30,ConfigSystemMotion=Save store in motion</actioncontainer>
        <actioncontainer id="t3" x="280.0" y="787.5">Remove Notification on Statusbar: 3 (Automagic)</actioncontainer>
        <actioncontainer id="t4" x="35.0" y="577.5">Notification on Statusbar: Webcam disabled House ID 2</actioncontainer>
        <connection from="t1" to="t2" type="NORMAL" sourcePosition="SOUTH" targetPosition="NORTH" />
        <connection from="t2" to="t4" type="NORMAL" sourcePosition="SOUTH" targetPosition="NORTH" />
        <connection from="t4" to="t3" type="NORMAL" sourcePosition="SOUTH" targetPosition="NORTH" />
      </flow>
      <flow type="flow">
        <name>Webcam enable motion</name>
        <group>Webcam</group>
        <enabled>true</enabled>
        <lastExecutionStartTime>1417459379523</lastExecutionStartTime>
        <lastExecutionEndTime>1417459399056</lastExecutionEndTime>
        <executionPolicy>PARALLEL</executionPolicy>
        <triggercontainer id="t1" x="-175.0" y="17.5">
          <trigger>WiFi Disconnected: SSID</trigger>
        </triggercontainer>
        <actioncontainer id="t2" x="-175.0" y="227.5">Sleep: 15s (allow device sleep)</actioncontainer>
        <conditioncontainer id="t3" x="-175.0" y="472.5">Active Network Type: Mobile</conditioncontainer>
        <actioncontainer id="t4" x="-175.0" y="682.5">HTTP Request: POST https://webcam.public.url/setSystemMotion application/x-www-form-urlencoded ReplySuccessPage=motion.htm,ReplyErrorPage=motion.htm,MotionDetectionEnable=1,MotionDetectionScheduleDay=30,MotionDetectionScheduleMode=0,MotionDetectionSensitivity=50,ConfigSystemMotion=Save store in response</actioncontainer>
        <actioncontainer id="t5" x="105.0" y="262.5">Notification on Statusbar: Webcam enabled House ID 3</actioncontainer>
        <actioncontainer id="t6" x="105.0" y="682.5">Remove Notification on Statusbar: 2 (Automagic)</actioncontainer>
        <connection from="t1" to="t2" type="NORMAL" sourcePosition="SOUTH" targetPosition="NORTH" />
        <connection from="t2" to="t3" type="NORMAL" sourcePosition="SOUTH" targetPosition="NORTH" />
        <connection from="t3" to="t4" type="TRUE" sourcePosition="SOUTH" targetPosition="NORTH" />
        <connection from="t3" to="t2" type="FALSE" sourcePosition="SOUTH" targetPosition="NORTH" />
        <connection from="t4" to="t5" type="NORMAL" sourcePosition="SOUTH" targetPosition="NORTH" />
        <connection from="t5" to="t6" type="NORMAL" sourcePosition="SOUTH" targetPosition="NORTH" />
      </flow>
    </data>
