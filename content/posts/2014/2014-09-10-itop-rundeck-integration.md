---
date: 2014-09-10
title: "iTop CMDB 2.0.x inventory provider for Rundeck 2.2.x"
---
Date: 2014-09-10 Tags: code, php

This PHP page allows to retrieve configuration items to populate Rundeck inventory:

```
<?php

  // iTop CMDB inventory for Rundeck.
  // Virtual servers have VIRT tag, physicals get PHYS tag.

  $host = "127.0.0.1";
  $db = "itop_srv";
  $user = "root";
  $pwd = "";

  $connect = mysql_connect($host, $user, $pwd);
  mysql_select_db($db);

  $query_physical = "SELECT
          view_Server.id AS id,
          view_Server.name AS hostname,
          view_Server.description AS description,
          view_Server.osfamily_name AS osName,
          view_Server.osversion_name AS osVersion,
          view_Server.location_name AS location,
          view_Server.business_criticity AS criticity,
          view_Server.status AS status,
          view_ApplicationSolution.name AS role
  FROM
          view_Server
  LEFT JOIN
          lnkapplicationsolutiontofunctionalci
  ON
          lnkapplicationsolutiontofunctionalci.functionalci_id = view_Server.id
  LEFT JOIN
          view_ApplicationSolution
  ON
          lnkapplicationsolutiontofunctionalci.applicationsolution_id = view_ApplicationSolution.id
  ORDER BY
          view_Server.name;
  ;";

  $query_virtual = "SELECT
          view_VirtualMachine.id AS id,
          view_VirtualMachine.name AS hostname,
          view_VirtualMachine.description AS description,
          view_VirtualMachine.osfamily_name AS osName,
          view_VirtualMachine.osversion_name AS osVersion,
          view_VirtualMachine.virtualhost_name AS location,
          view_VirtualMachine.business_criticity AS criticity,
          view_VirtualMachine.status AS status,
          view_ApplicationSolution.name AS role
  FROM
          view_VirtualMachine
  LEFT JOIN
          lnkapplicationsolutiontofunctionalci
  ON
          lnkapplicationsolutiontofunctionalci.functionalci_id = view_VirtualMachine.id
  LEFT JOIN
          view_ApplicationSolution
  ON
          lnkapplicationsolutiontofunctionalci.applicationsolution_id = view_ApplicationSolution.id
  ORDER BY
          view_VirtualMachine.name;
  ;";

  $result_physical = mysql_query($query_physical, $connect);
  $result_virtual = mysql_query($query_virtual, $connect);

  // output
  header('Content-type: text/xml');

  echo "<project>\n";
  echo "<!-- PHYSICAL -->\n";

  // returns physicals
  while($row = mysql_fetch_object($result_physical))
  {
          $arch=explode(" ",$row->osVersion);
          if ($row->status == "production") {
                  $status = "ON";
          } elseif ($row->status == "obsolete") {
                  $status = "OFF";
          } else {
                  $status = "STATUS_UNKNOWN";
          }

          echo "    <node name=\"$row->hostname\" type=\"node\"\n";
          echo "        description=\"" . utf8_encode($row->description) . "\"\n";
          echo "        hostname=\"$row->hostname\"\n";
          echo "        username=\"root\"\n";
          echo "        osFamily=\"$row->osName\"\n";
          echo "        osName=\"$arch[0]\"\n";
          echo "        osVersion=\"$arch[1]\"\n";
          echo "        osArch=\"$arch[2]\"\n";
          echo "        tags=\"$status,PHYS,$arch[0],$arch[1],$arch[2],$row->criticity,ROOM_$row->location,$row->role\"\n";
          echo "        file-copy-destination-dir=\"/var/tmp/\"\n";
          echo "    />\n";
  }

  echo "<!-- VIRTUAL -->\n";

  // returns virtuals
  while($row = mysql_fetch_object($result_virtual))
  {
          $arch=explode(" ",$row->osVersion);
          if ($row->status == "production") {
                  $status = "ON";
          } elseif ($row->status == "obsolete") {
                  $status = "OFF";
          } else {
                  $status = "STATUS_UNKNOWN";
          }

          echo "    <node name=\"$row->hostname\" type=\"node\"\n";
          echo "        description=\"" . utf8_encode($row->description) . "\"\n";
          echo "        hostname=\"$row->hostname\"\n";
          echo "        username=\"root\"\n";
          echo "        osFamily=\"$row->osName\"\n";
          echo "        osName=\"$arch[0]\"\n";
          echo "        osVersion=\"$arch[1]\"\n";
          echo "        osArch=\"$arch[2]\"\n";
          echo "        tags=\"$status,VIRT,$arch[0],$arch[1],$arch[2],$row->criticity,ROOM_$row->location,$row->role\"\n";
          echo "        file-copy-destination-dir=\"/var/tmp/\"\n";
          echo "    />\n";
  }

  echo "</project>\n";
  ?>
```
