#!/bin/sh


menu_list()
{
    echo  ;
    echo -e "###################\\033[1;31m请选择备份类型\\033[0m####################"
    echo -e "**-------\\033[1;34m1\\033[0m). 启动服务------------------------------**"
    echo -e "**-------\\033[1;34m2\\033[0m). 重启服务------------------------------**"
    echo -e "**-------\\033[1;34m3\\033[0m). 操作说明------------------------------**"
    echo -e "**-------\\033[1;34m4\\033[0m). 退出----------------------------------**"
    echo -e "*****************************************************"
    read -p "请选择备份类型[1~4]:" list
    
    if [ -z "${list//[0-9]/}" ]
    then
        return $list
    else
        return 0
    fi
    
}

##启动nginx
start_nginx(){
	/usr/local/nginx/sbin/nginx
	if [ $? -ne 0 ]
	then
		echo "nginx启动失败"
	fi
	echo "nginx启动成功"
}

##启动php
start_php(){
	/usr/local/php/sbin/php-fpm
	
	if [ $? -ne 0 ]
	then
		echo "php启动失败"
	fi
	echo "php启动成功"
}
##启动mysql
start_mysql(){
	service mysql start
	if [ $? -ne 0 ]
	then
		echo "mysql启动失败"
	fi
	echo "mysql启动成功"
}

##停止nginx
stop_nginx(){
	/usr/local/nginx/sbin/nginx -s stop
	if [ $? -ne 0 ]
	then
		echo "nginx停止失败"
	fi
	echo "nginx停止成功"
}

##停止php
stop_php(){
	pkill -9 php-fpm
	
	if [ $? -ne 0 ]
	then
		echo "php停止失败！"
	fi
	echo "php停止成功！"
}
##停止mysql
stop_mysql(){
	service mysql stop
	if [ $? -ne 0 ]
	then
		echo "mysql停止失败！"
	fi
	echo "mysql停止成功"
}

## help
readme()
{
    echo  "1.启动mysql,php,nginx服务。"
    echo  "2.重启mysql,php,nginx服务。"
    echo  "3.详细说明"
    echo  "4.退出。"
}    

while [ 1 ]
do
	service iptables stop
	menu_list
	select=$?
	case $select in
	1)
		start_nginx
		start_php
		start_mysql
		;;
	2)
		stop_nginx
		stop_php
		stop_mysql
		start_nginx
		start_php
		start_mysql
		;;
	3)
		readme ;;
	4)
		exit 1 ;;
	*)
		echo "请在1~4之间选择服务类型" ;;
	esac
done
