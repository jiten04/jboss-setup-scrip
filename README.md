# jboss-setup-scrip
installation of jboss-as-7.1.1.Final on Ubuntu
#!/bin/bash
#This is the script for installaion jboss-7.1.1.Final
#in centos you have to change you jboss installation 
#path localtion according to your chooes
#installing OpenJDK

apt-get update -y
apt-get install openjdk-7-jdk -y 
apt-get install wget -y 
apt-get install zip -y 

#following wget to download jboss-as-7.1.1.Final.zip:

wget http://download.jboss.org/jbossas/7.1/jboss-as-7.1.1.Final/jboss-as-7.1.1.Final.zip

#following unzip command to finally install jboss-as-7.1.1.Final in the 
unzip jboss-as-7.1.1.Final.zip -d /usr/share/

#assign the appropriate ownership to the installation directory for the newly 
#created jboss user by issuing the command:
chown -fR ubuntu.ubuntu /usr/share/jboss-as-7.1.1.Final/

#lets change directories to the JBoss bin directory
cd /usr/share/jboss-as-7.1.1.Final/bin

cat > /etc/init.d/jboss <<- "EOF"
#!/bin/bash
### BEGIN INIT INFO
# Provides:          jboss
# Required-Start:    $local_fs $remote_fs $network $syslog
# Required-Stop:     $local_fs $remote_fs $network $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start/Stop JBoss
### END INIT INFO
# chkconfig: 35 92 1

JBOSS_HOME=/usr/share/jboss-as-7.1.1.Final

AS7_OPTS="$AS7_OPTS -Dorg.apache.tomcat.util.http.ServerCookie.ALLOW_HTTP_SEPARATORS_IN_V0=true"
AS7_OPTS="$AS7_OPTS -Djboss.bind.address.management=0.0.0.0"
AS7_OPTS="$AS7_OPTS -Djboss.bind.address=0.0.0.0"

case "$1" in
    start)
        /bin/echo "Starting JBoss ..."
        su - root -c "sudo ${JBOSS_HOME}/bin/standalone.sh $AS7_OPTS > /dev/null & "
    ;;
    stop)
        /bin/echo "Stopping JBoss ..."
        su - root -c "${JBOSS_HOME}/bin/jboss-cli.sh --connect command=:shutdown"
    ;;
    *)
        /bin/echo "Usage: /etc/init.d/jboss1 {start|stop}"; exit 1;
    ;;
esac

exit 0
EOF

chmod +x /etc/init.d/jboss

#deploy application on jboss
mv /home/ubuntu/spring.war /usr/share/jboss-as-7.1.1.Final/standalone/deployments

#lets start jboss service
service jboss start

#Install Linux Apache MySQL PHP (LAMP) Server
apt-get install lamp-server^

#Install PHPMyAdmin
apt-get install phpmyadmin
