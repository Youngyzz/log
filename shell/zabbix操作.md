# zabbix server容器操作，支持run/start/stop/rm/inspect/exec等命令
```sh
#!/bin/bash

show_zabbix(){
docker ps --format "{{.Image}} {{.Names}} {{.Status}}" \
 | awk 'BEGIN{n=0;} $1 ~ /(zabbix)|(mysql)/ {n=n+1;printf "%-10s %-2s %-5s %-30s %-2d\n",$3,$4,$5,$2, n}' | tee /dev/tty | awk '{print $4}'
}

exec_zabbix(){
        cmd=/bin/bash
        if [ -n $2 ]; then
                cmd=$2
        fi
        echo $1 $2
        docker exec -it $1 $cmd
}

echo "============欢迎使用zabbix系统============"
echo "当前服务器"
array=($(show_zabbix))
echo ${array[2]}
#declare -i len
len=${#array[@]}

read -p "请输入命令(帮助:help)" op opt
case ${op} in
        exec)
                while [ -z "$opt" ]; do
                        read -p "<序号> [shell命令]:" opt
                done
                declare -i x
                x=${opt% *}
                #echo ${opt#* }
                #echo ${array[$x-1]}
                if ((x<len));then
                        #FIXED ME:当参数个数为1时，下面${opt#* }存在问题，
                        exec_zabbix ${array[$x-1]} ${opt#* }
                else
                        echo "输入无效:" $x
                fi
                ;;
        *)
                echo -h
                ;;
esac
```