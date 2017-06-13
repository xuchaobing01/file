#!/bin/sh


menu_list()
{
    echo  ;
    echo -e "###################\\033[1;31m请选择备份类型\\033[0m####################"
    echo -e "**-------\\033[1;34m1\\033[0m). 备份所有数据库------------------------**"
    echo -e "**-------\\033[1;34m2\\033[0m). 备份单个数据库包括(表、存储过程、函数)**"
    echo -e "**-------\\033[1;34m3\\033[0m). 备份单个数据库下的一个或多个表--------**"
    echo -e "**-------\\033[1;34m4\\033[0m). 备份单个数据库下的存储过程和函数------**"
    echo -e "**-------\\033[1;34m5\\033[0m). 操作说明------------------------------**"
    echo -e "**-------\\033[1;34m6\\033[0m). 退出----------------------------------**"
    echo -e "*****************************************************"
    read -p "请选择备份类型[1~6]:" list
    
    if [ -z "${list//[0-9]/}" ]
    then
        return $list
    else
        return 0
    fi
    
}


## backup all database case=1
bk_alldb()
{
    read -sp "请输入mysql root用户密码:" pw
    echo -e "\n开始备份所有数据库，请稍后... ..."
    
    arr=`mysql -uroot -p"$pw" -s -w -e 'show databases'`
    dir=`pwd`
    time=`date +%Y-%m-%d`
    
    if [ ! -d $time ]
    then
        mkdir $time
    fi

    for dbname in $arr
    do
        if [ "$dbname" != "mysql" -o "$dbname" != "information_schema" -o "$dbname" != "test" ]
        then
            mysqldump -uroot -p"$pw" -R --default-character-set=utf8 "$dbname" > $dir/$time/"$dbname"_"$time".sql
            if [ $? -ne 0 ]
            then
                echo "$dbname 备份出错!"
                exit 1
            fi
        fi
    done
    echo -e "备份所有库完成,^_^!\n"
}

## backup one db case=2
bk_onedb()
{
    read -p "请输入备份的数据名称:" dbname
    read -p "请输入用户名称:" username
    read -sp "请输入用户密码:" pw
    echo -e "\n开始备份数据库，请稍后... ..."
    
    dir=`pwd`
    time=`date +%Y-%m-%d`
    
    if [ ! -d $time ]
    then
        mkdir $time
    fi
    
    mysqldump -u"$username" -p"$pw" -R --default-character-set=utf8 "$dbname" > $dir/$time/"$dbname"_"$time".sql
    if [ $? -ne 0 ]
    then
        echo "$dbname 备份出错!"
        exit 1
    fi

    echo -e "备份单个数据库完成,^_^!\n"
}


## backup table case=3
bk_table()
{
    read -p "请输入备份的数据库名称:" dbname
    read -p "请输入用户名称:" username
    read -s -p "请输入用户密码:" pw
    read -p "请输入要备份的表名称,如果是多个表以空格分割开:" tablename
    echo -e "\n开始备份数据库，请稍后... ..."

    dir=`pwd`
    time=`date +%Y-%m-%d`
    
    if [ ! -d $time ]
    then
        mkdir $time
    fi
    
    mysqldump -u"$username" -p"$pw" --default-character-set=utf8 "$dbname" --tables $tablename > $dir/$time/"$dbname"_"$time".sql
    if [ $? -ne 0 ]
    then
        echo "$dbname 备份出错!"
        exit 1
    fi

    echo -e "备份表完成,^_^!\n"
}

## backup table case=4
bk_dbproc()
{
    read -p "请输入备份的数据库名称:" dbname
    read -p "请输入用户名称:" username
    read -sp "请输入用户密码:" pw
    echo -e "\n开始备份数据库，请稍后... ..."
    
    dir=`pwd`
    time=`date +%Y-%m-%d`
    
    if [ ! -d $time ]
    then
        mkdir $time
    fi
    
    mysqldump -u"$username" -p"$pw" -t -d -R --default-character-set=utf8 "$dbname" > $dir/$time/"$dbname"_"$time".sql
    if [ $? -ne 0 ]
    then
        echo "$dbname 备份出错!"
        exit 1
    fi

    echo -e "备份存储过程函数完成,^_^!\n"
}

## help
readme()
{
    echo  "1.选择1则备份MySql下的所有数据库(不包含系统创建的默认库)，备份结果保存在当前目录下以当天日期为目录的文件夹中。"
    echo  "2.选择2则备份输入的数据库，备份结果包含该库的存储过程和函数 ，保存在日期为名的文件夹中。"
    echo  "3.选择3则备份指定数据库下的一个或者多个表。"
    echo  "4.选择4则只备份指定数据下存储过程和函数不包括建表语句和数据。"
    echo  "5.所有备份结果的默认字符集都为utf8。"
}


## main
while [ 1 ]
do    
    menu_list;
    select=$?    
    case "$select" in
    1)
        bk_alldb;;
    2)
        bk_onedb;;
    3)
        bk_table;;
    4)
        bk_dbproc;;
    5)
        readme;;
    6)
        exit 1 ;;
    *)
        echo "请在1～6之间选择备份类型!";;
    esac
done