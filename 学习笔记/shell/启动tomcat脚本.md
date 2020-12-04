```shell
#By CaoKang
######### set project info ######
APP_DIR="/usr/local/smart_uat_bau"
STARTTOMCAT=$APP_DIR/bin/startup.sh

usage(){
     echo "Usage : sh smart_uat_bau.sh [start | stop | restart | status]"
     exit 1
}

running_state(){
     pid=`ps -ef|grep $APP_DIR |grep -v grep |awk '{print $2}'`
     if [ -z "${pid}" ];then
          return 1
     else
          return 0
     fi
}

start(){
     running_state
     if [ $? -eq "0" ];then
          echo "${APP_DIR} is already running .pid=${pid}."
     else
		  $STARTTOMCAT
          echo "${APP_DIR} start success..."
     fi
}

stop(){
     running_state
     if [ $? -eq "0" ];then
	   echo "${APP_DIR} stop"
           kill $pid
     else
          echo "${APP_DIR} is not running "
     fi
}

status(){
     running_state
     if [ $? -eq "0" ];then
          echo "${APP_DIR} is running... PID is ${pid} "
     else
          echo "${APP_DIR} is not running "
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

