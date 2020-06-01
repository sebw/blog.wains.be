---
date: 2012-01-06
title: "ActiveMQ 5.4.x install under RHEL 5.x"
---







categories:
- ActiveMQ
- Howto
- Linux


_Tested with ActiveMQ 5.4.3, Red Hat Linux Enterprise 5.7 64 bits with Sun JVM 1.5

ActiveMQ 5.5.x requires JVM 1.6_

The following is a simple copy and paste howto. Simply adapt the install variables and you're good to go.


**Let's declare some variables for the install process :**

`AMQDIR="/usr/local"
VERSION="5.4.3"`


**Download and installation :**

`cd /root
wget http://apache.cu.be//activemq/apache-activemq/$VERSION/apache-activemq-$VERSION-bin.tar.gz
cp /root/apache-activemq-$VERSION-bin.tar.gz $AMQDIR
cd $AMQDIR
tar xvzf apache-activemq-$VERSION-bin.tar.gz
chown root. apache-activemq* -R
ln -f -s apache-activemq-$VERSION activemq`


**Configuration :**

`sed -i 's#ACTIVEMQ_HOME.*#ACTIVEMQ_HOME="$AMQDIR/activemq"#g' $AMQDIR/activemq/bin/linux-x86-64/activemq

sed -i 's#set.default.ACTIVEMQ_HOME=.*#set.default.ACTIVEMQ_HOME=$AMQDIR/activemq#g' $AMQDIR/activemq/bin/linux-x86-64/wrapper.conf

sed -i 's#set.default.ACTIVEMQ_BASE=.*#set.default.ACTIVEMQ_BASE=$AMQDIR/activemq#g' $AMQDIR/activemq/bin/linux-x86-64/wrapper.conf`


**Init script and making ActiveMQ start at boot :**

`ln -s $AMQDIR/activemq/bin/linux-x86-64/activemq /etc/init.d/activemq
chkconfig --add activemq
chkconfig activemq on
service activemq start`


**Logs :**

`tail -f /usr/local/activemq/data/wrapper.log`


**Accessing the admin section :**

http://$SERVER:8161/admin/index.jsp
