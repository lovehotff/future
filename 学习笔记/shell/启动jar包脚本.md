```shell
#By CK
######### set project info ######
APP_DIR="/usr/local/smart_uat_proj_new"
APP_NAME=smart-web.jar
LOG_FILE="/mnt/ntfs/ccms/ccmslog/10.142.143.121SMART_UAT_PORJ_NEW/smart-web.log"
#### set java env ####
JAVA_OPTS="-Xms512m -Xmx512m"

usage(){
     echo "Usage : sh smart_porj_new.sh [start | stop | restart | status]"
     exit 1
}

running_state(){
     pid=`ps -ef|grep $APP_NAME |grep -v grep |awk '{print $2}'`
     if [ -z "${pid}" ];then
          return 1
     else
          return 0
     fi
}

start(){
     running_state
     if [ $? -eq "0" ];then
          echo "${APP_NAME} is already running .pid=${pid}."
     else
          nohup java -jar $JAVA_OPTS $APP_DIR/$APP_NAME > $LOG_FILE 2>&1 &
          echo "${APP_NAME} start success..."
     fi
}

stop(){
     running_state
     if [ $? -eq "0" ];then
	   echo "${APP_NAME} stop"
           kill $pid
     else
          echo "${APP_NAME} is not running "
     fi
}

status(){
     running_state
     if [ $? -eq "0" ];then
          echo "${APP_NAME} is running... PID is ${pid} "
     else
          echo "${APP_NAME} is not running "
     fi
}

restart(){
     stop
     sleep 10
     start
}

case "$1" in
     "start") start ;;
     "stop") stop ;;
     "status") status ;;
     "restart") restart ;;
     *) usage ;;
esac 

```

