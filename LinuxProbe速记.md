

0001.网卡配置信息
    设备类型：TYPE=Ethernet
    地址分配模式：BOOTPROTO=static
    网卡名称：NAME=eno16777736
    是否启动：ONBOOT=yes
    IP地址：IPADDR=192.168.10.10
    子网掩码：NETMASK=255.255.255.0
    网关地址：GATEWAY=192.168.10.1
    DNS地址：DNS1=192.168.10.1
	
0002.配置yum软件仓库
    使用Vim编辑器创建一个名为rhel7.repo的新配置文件（文件名称可随意，但后缀必须为.repo），逐项写入下面加粗的配置参数并保存退出（不要写后面的中文注释）。
    [rhel-media] 	# Yum软件仓库唯一标识符，避免与其他仓库冲突。
	name=linuxprobe	   # Yum软件仓库的名称描述，易于识别仓库用处。
	baseurl=file:///media/cdrom       # 提供的方式包括FTP(ftp://..),HTTP(http://..),本地(file:///..)
	enabled=1      # 设置此源是否可用；1为可用，0为禁用。
	gpgcheck=1     # 设置此源是否校验文件；1为校验，0为不校验。
	gpgkey=file:///media/cdrom/RPM-GPG-KEY-redhat-release      # 若上面参数开启校验，那么请指定公钥文件地址。
	
	之后按配置参数的路径挂载光盘，如果希望开机就挂载光盘的话，就把光盘挂载信息写入到/etc/fstab文件中。
	可以使用“yum install httpd -y”命令检查Yum软件仓库是否已经可用。
	
	yum常用命令：
	yum clean all			# 清除所有仓库缓存
	yum list all			# 列出仓库中所有软件包
	yum repolist all			# 列出所有仓库	
	yum groupinstall 软件包组		# 安装指定的软件包组
	yum groupremove 软件包组		# 移除指定的软件包组
	yum groupinfo 软件包组		# 查询指定的软件包组信息
	yum update 软件包名称		# 升级指定软件包
	yum info 软件包名称		# 查看指定软件包信息
	
	rpm常用命令：
	rpm -ivh 文件.rpm			# 安装软件并显示安装过程
	rpm -Uvh 文件.rpm			# 用软件包升级软件并显示升级过程
	rpm -e 软件包名			# 卸载软件包；rpm -e --nodeps强制卸载
	rpm -qpi 文件.rpm			# 查询软件描述信息
	rpm -qpl 文件.rpm			# 查询软件包的文件信息
	rpm -qf 文件			# 查询文件属于那个rpm包
	rpm -qa				# 列出所有已经安装的软件包
	
	
0003.管道符的部分应用
    修改指定用户密码：echo "密码" | passwd --stadin 用户名
    给指定用户发送邮件：echo "邮件内容" | mail -s "邮件主题" 用户名
    发送邮件时指定结束标记：mail -s "邮件主题" root@linuxprobe.com << over
        # 改命令回车后开始输入邮件内容，直到遇到over分解符,结束邮件内容的输入并发送

0004.输入输出重定向
    标准输入重定向(stdin,文件描述符为0)：默认从键盘输入，也可以从其它文件或命令中输入
    标准输出重定向(stdout,文件描述符为1)：默认输出到屏幕
    错误输出重定向(stderr,文件描述符为2)：默认输出到屏幕
	
    输入重定向常用操作：
    命令 < 文件			# 将文件作为命令的标准输入
    命令 << 分界符			# 从标准输入中读入，直到遇见分界符才停止 
    命令 < 文件1 > 文件2		# 将文件1作为命令的标准输入并将标准输出到文件2
    
    输出重定向常用操作：
    命令 > 文件			# 将标准输出重定向到一个文件中（清空原有文件的数据）
    命令 2> 文件			# 将错误输出重定向到一个文件中（清空原有文件的数据）
    命令 >> 文件			# 将标准输出重定向到一个文件中（追加到原有内容的后面）
    命令 2>> 文件			# 将错误输出重定向到一个文件中（追加到原有内容的后面）
    命令 &>> 文件			# 将标准输出与错误输出写入到同一个文件中（追加到原有内容的后面）
    命令 >> 文件 2>&1			# 将标准输出与错误输出写入到同一个文件中（追加到原有内容的后面）

0005.dd命令
    功能：它能够让用户按照指定大小的数据块和个数来复制文件的内容
    
    dd if=/dev/zero of=560_file count=1 bs=560M
    参数及作用：
    	if         # 输入的文件名称
    	of         # 输出的文件名称
    	bs         # 设置每个“块”的大小
    	count      # 设置要复制的“块”的个数
    
    功能举例：dd if=/dev/cdrom of=RHEL-Server-7.0-x86-Linux.iso  # 将光盘中制作为iso文件
    
0006.tar命令
    功能：对文件进行打包压缩或解压
    
    压缩
    tar -czvf etc.tar.gz /etc/		# 对目录/etc/进行打包，并压缩为gzip格式，显示打包过程，压缩名为etc.tar.gz
    tar -cjvf etc.tar.bz2 /etc/		# 对目录/etc/进行打包，并压缩为bzip2格式，显示打包过程，压缩名为etc.tar.bz2
    解压
    tar -xzvf etc.tar.gz -C /tmp/   	# 对etc.tar.gz文件进行解压，并指定解压目录为/tmp/
    tar -xjvf etc.tar.bz2		# 对etc.tar.bz2文件进行解压，解压目录为当前目录
    
    权限保留
    tar -czvf etc.tar.gz -P /etc/       # 对/etc目录进行压缩并使用大写的P参数保留文件原始的权限和属性
    
    查看
    tar -tf etc.tar.gz              # 查看压缩包内的文件(不解压)
    
0007.find命令
    格式：find 指定路径 寻找条件 操作
    
    find /etc/ -name "host" -print		# 在/etc/目录下查找文件名包含host的文件，并打印
    
    参数：
    -name				# 匹配名称
    -perm				# 匹配权限（mode为完全匹配，-mode为包含即可）
    	# find . -perm 775
    	# find /tmp/ -perm -7 | xargs chmod o-w
    -user				# 匹配所有者
    -group				# 匹配所有组
    -mtime -n +n		# 匹配修改内容的时间（-n值n天以内，+n指n天以前）
    -atime -n +n		# 匹配访问文件的时间
    -ctime -n +n		# 匹配修改文件权限的时间
    -nouser		# 匹配无所有者的文件
    -nogroup		# 匹配无所有组的文件
    -newer f1 !f2	# 匹配比文件f1新但比f2旧的文件
    --type b/d/c/p/l/f	# 匹配文件类型（块设备、目录、字符设备、管道、链接文件、文本文件）
    -size		# 匹配文件的大小（+50KB为查找超过50KB的文件，而-50KB为查找小于50KB的文件）
    -prune		# 忽略某个目录
    -exec ...... {} \;	# find后面可跟用于进一步处理搜索结果的命令
    	例子：find /home/ -user nano -exec cp -a {} /tmp/test/ \;
    	# 查找/home/目录下所有用户为nano的文件，并将文件拷贝到/tmp/test/目录下
    	# cp命令-a参数的功能为：保留原始文件属性、递归持续复制(对于目录)、若为链接文件则保留链接文件的属性
    
0008.touch命令
    touch -d "2018-09-08 11:41" 文件      # 将文件的读取时间与修改时间同时修改为指定时间
    
    参数：
    	-a     # 仅修改“读取时间”（atime）
    	-m     # 仅修改“修改时间”（mtime）
    
0009.tr命令
    功能：用于替换文本文件中的字符；
    格式：tr 原始字符　目标字符
    
    cat /etc/hosts | tr [a-z] [A-Z] > /tmp/test/new.txt
    
0009.Redhat系列图形化与文本界面切换
    切换为文本界面：
    	ln -sf /lib/systemd/system/multi-user.target /etc/systemd/system/default.target
    切换为图形界面：
    	ln -sf /lib/systemd/system/graphical.target /etc/systemd/system/default.target
    
0010.Redhat系列服务查看
    systemctl enable httpd.service          # 设置httpd服务开机自动启动
    systemctl disable httpd.service         # 设置httpd服务开机不启动
    systemctl is-enable httpd.service       # 查看httpd服务是否为开机自动启动
    systemctl list-unit-files --type=service    # 查看各个级别下服务的启动与禁用情况
    
    系统服务管理补充：
    pidof命令
    说明：
        * 每个进程的进程号码值(PID)是唯一的，所以可以通过PID来区分不同的进程
        * pidof命令用于查询某个指定服务进程的PID值
    格式：pidof  [参数]  服务名称

    kill命令
    说明：kill命令用于终止某个指定PID的服务进程
    格式：kill  [参数]  进程PID

    killall命令
    说明：
        * 通常来说，复杂软件的服务程序会有很多进程协同为客户提供服务
        * killall命令用于终止某个指定名称的服务所对应的全部进程
    格式：killall  [参数]  服务名称
    例如：
        [root@linuxprobe ~]# pidof httpd
        13581 13580 13579 13578 13577 13576
        [root@linuxprobe ~]# killall httpd
        [root@linuxprobe ~]# pidof httpd
        [root@linuxprobe ~]#


    
0011.Redhat系列root密码重置
    在Linux的引导启动项界面，按下ｅ键进入内核编辑界面，找到linux16这行，并在改行最后面追加 rd.break　参数(说明：改行的内容中包含了LANG=en_US.UTF-8 或　LANG=zh_CN.UTF-8)，然后按下 Ctrl + x 组合键来运行修改过的内核，之后会进入系统的紧急救援模式，输入如下命令修改系统root密码
    mount -o remount,rw /sysroot/	# 以读写模式重新挂载/sysroot/目录	
    chroot /sysroot/        # 以目录/sysroot/作为当前系统的根目录位置
    passwd                  # 输入该命令回车，然后会提示输入新密码
    touch /.autorelabel     # 系统启动时会判断/.autorelabel文件是否存在，存在则会调用fixfiles命令对整个文件系统进行重新标记(relabeling);如果该机的selinux处于关闭状态则不需要执行该条命令
    exit
    reboot
    
0012.shell部分功能01
    shell脚本及参数的判断
    	$0		# 当前脚本的名称
    	$?		# 显示上一次命令的返回值
    	$1		# 脚本第一个位置的参数值（如：example.sh name  , 这里的name就是$1的值）
    	$n		# 脚本第n个位置的参数值
    
    测试语句
    功能：使用测试语句对目标文件进行判断，然后通过Shell解释器的内设 $? 变量显示上一条命令执行后的返回值；如果返回值为０则判断正确，如果返回值为非零的值，则意味着判断不正确
    格式：　[ 条件表达式 ]		# 注意，两边都有一个空格哦
    文件测试所用的参数：
    	-d		# 判断文件是否为目录文件
    	-e		# 判断文件是否存在
    	-f		# 判断是否为一般文件
    	-r		# 判断当前用户是否有权限读取
    	-w		# 判断当前用户是否有权限写入
    	-x		# 判断当前用户是否有权限执行
    简单应用：
    	nano@GBooter:~$ [ -d /etc/fstab ]		# 判断/etc/fstab是否为目录
    	nano@GBooter:~$ echo $?
    	1
    	nano@GBooter:~$ [ -f /etc/fstab ]		# 判断/etc/fstab是否为文件
    	nano@GBooter:~$ echo $?
    	0
    	nano@GBooter:~$ 
    
    测试语句与逻辑语句的应用
    逻辑语句用于对测试结果进行逻辑分析，根据测试结果可实现不同的效果
    01:Shell终端中逻辑"与"的运算符号是 && ，它表示当前面的命令执行成功后才会执行它后面的命令
        [root@linuxprobe ~]# [ -e /dev/cdrom ] && echo "DVD已经放入"		
        DVD已经放入
        [root@linuxprobe ~]# 
    
    02:Shell终端中逻辑"或"的运算符号是 || ，它表示当前面的命令执行失败后才会执行它后面的命令
        [root@linuxprobe ~]# echo $USER
        root
        [root@linuxprobe ~]# [ $USER = root ] || echo "user,前面的命令执行失败了哦"
        [root@linuxprobe ~]# su - linuxprobe
        上一次登录：六 9月  8 18:23:09 CST 2018pts/0 上
        [linuxprobe@linuxprobe ~]$ [ $USER = root ] || echo "user,前面的命令执行失败了哦"
        user,前面的命令执行失败了哦
        [linuxprobe@linuxprobe ~]$ 
        
    03:Shell终端中逻辑"非"的运算符号是　! ，它表示把条件测试中的判断结果取相反值；也就是，如果原本测试的结果是正确的，则将其变成错误的，原本测试的结果是错误的，则将其变成正确的
    判断用户是否 root 的例子
    执行顺序说明：先判断当前用户的 USER 变量名称是否等于 root，然后用逻辑运算符"非"进行取反操作，效果就是变成了判断当前用户是否为非管理员用户了；最后若条件成立则会根据逻辑"与"运算符输出 user　字样；或条件不满足则会通过逻辑"或"运算符输出root字样；简单来说就是，如果前面的 && 不成立才会执行后面的 || 符号
        [root@linuxprobe ~]# [ ! $USER = root ] || echo "administrator,我就是root用户哦"
        administrator,我就是root用户哦
        [root@linuxprobe ~]# [ ! $USER = root ] && echo "user" || echo "root"
        root
        [root@linuxprobe ~]# su - linuxprobe
        上一次登录：六 9月  8 18:25:22 CST 2018pts/0 上
        [linuxprobe@linuxprobe ~]$ [ ! $USER = root ] && echo "user" || echo "root"
        user
        [linuxprobe@linuxprobe ~]$
    
    04:可用的整数比较运算符
    	-eq		# 是否等于
    	-ne		# 是否不等于
    	-gt		# 是否大于
    	-lt		# 是否小于
    	-le		# 是否等于或小于
    	-ge		# 是否等于或大于
    	=		# 比较字符串内容是否相同
    	!=		# 比较字符串内容是否不同
    	-z		# 判断字符串内容是否为空
    	
    简单使用
    	[root@linuxprobe ~]# [ 10 -gt 10 ]
    	[root@linuxprobe ~]# echo $?
    	1
    	[root@linuxprobe ~]# [ 10 -eq 10 ]
    	[root@linuxprobe ~]# echo $?
    	0
    	[root@linuxprobe ~]# 
    日常使用
    	[root@linuxprobe ~]# free -h
    			      total        used        free      shared  buff/cache   available
    	Mem:           278M        134M        5.1M        4.7M        138M        106M
    	Swap:          2.0G          0B        2.0G
    	[root@linuxprobe ~]# free -m | grep Mem
    	Mem:            278         134           4           4         138         106
    	[root@linuxprobe ~]# FreeMem=`free -m | grep Mem | awk '{print $4}'`
    	[root@linuxprobe ~]# echo $FreeMem 
    	4
    	[root@linuxprobe ~]# [ $FreeMem -lt 1024 ] && echo "内存不足"
    	内存不足
    	[root@linuxprobe ~]# 
    	[root@linuxprobe ~]# echo $LANG
    	zh_CN.UTF-8
    	[root@linuxprobe ~]# [ $LANG != "en.US" ] && echo "Not en.US"
    	Not en.US
    	[root@linuxprobe ~]# 
    
0013.shell测试语句之if
    检查主机是否在线：
    说明：ping命令通过-c参数来规定尝试的次数，并使用-i参数来定义每个数据包的发送间隔，以及使用-W参数定义等待超时时间
        [root@linuxprobe test]# cat chkhost.sh 
        #!/bin/bash
        echo "主机活性检测开始"
        ping -c 3 -i 0.2 -W 3 $1 &> /dev/null		#　将正确和错误输出都输出到/dev/null中
        if [ $? -eq 0 ]
        then
        	echo "Host $1 is on-line."
        else
        	echo "Host $1 is off-line."
        fi
        echo "主机活性检测完成"
        [root@linuxprobe test]# bash chkhost.sh 192.168.10.1
        主机活性检测开始
        Host 192.168.10.1 is on-line.
        主机活性检测完成
        [root@linuxprobe test]# bash chkhost.sh 192.168.10.3
        主机活性检测开始
        Host 192.168.10.3 is off-line.
        主机活性检测完成
        [root@linuxprobe test]# 	
    
    检测用户输入分数级别
        [root@linuxprobe test]# cat chkscore.sh 
        #!/bin/bash
        read -p "请输入你的成绩(0-100): " Grade		# Grade用来接收用户输入，-p参数用于显示提示信息
        
        if [ $Grade -ge 85 ] && [ $Grade -le 100 ];
        then
        	echo "$Grade is Excellent"
        elif [ $Grade -ge 70 ] && [ $Grade -le 84 ];
        then
        	echo "$Grade is Pass"
        else
        	echo "$Grade is Fail"
        fi
        [root@linuxprobe test]# 
        [root@linuxprobe test]# bash chkscore.sh 
        请输入你的成绩(0-100): 90
        90 is Excellent
        [root@linuxprobe test]# bash chkscore.sh 
        请输入你的成绩(0-100): 84
        84 is Pass
        [root@linuxprobe test]# bash chkscore.sh 
        请输入你的成绩(0-100): 59
        59 is Fail
        [root@linuxprobe test]# 
    
001４.shell测试语句之for
    从文件中获取用户名批量添加用户
        [root@linuxprobe test]# cat users.txt 
        andy
        barry
        carl
        duke
        eric
        george
        [root@linuxprobe test]# cat Example.sh 
        #!/bin/bash
        read -p "请输入用户密码：" Passwd
        
        for Uname in `cat users.txt`
        do
        	id $Uname &> /dev/null
        	if [ $? -eq 0 ]
        	then
        		echo "用户 $Uname 已经存在，不需要创建"
        	else
        		useradd $Uname &> /dev/null
        		echo "$Passwd" | passwd --stdin $Uname &> /dev/null
        		if [ $? -eq 0 ]
        		then
        			echo "$Uname, 用户创建成功"
        		else
        			echo "$Uname, 用户创建失败"
        		fi
        	fi
        done
        [root@linuxprobe test]# 
        [root@linuxprobe test]# bash Example.sh 
        请输入用户密码：123456
        andy, 用户创建成功
        barry, 用户创建成功
        carl, 用户创建成功
        duke, 用户创建成功
        eric, 用户创建成功
        george, 用户创建成功
        [root@linuxprobe test]# grep /bin/bash /etc/passwd
        root:x:0:0:root:/root:/bin/bash
        mooc:x:1000:1000:mooc:/home/mooc:/bin/bash
        linuxprobe:x:1001:1001::/home/linuxprobe:/bin/bash
        andy:x:1002:1002::/home/andy:/bin/bash
        barry:x:1003:1003::/home/barry:/bin/bash
        carl:x:1004:1004::/home/carl:/bin/bash
        duke:x:1005:1005::/home/duke:/bin/bash
        eric:x:1006:1006::/home/eric:/bin/bash
        george:x:1007:1007::/home/george:/bin/bash
        [root@linuxprobe test]# 
        
    从文件中获取用户名，批量删除用户
        [root@linuxprobe test]# cat users.txt 
        andy
        barry
        carl
        duke
        eric
        george
        [root@linuxprobe test]# cat Deluser.sh 
        #!/bin/bash
        read -p "请输入要删除的用户列表文件：" users
        
        for Uname in `cat $users`
        do
        	id $Uname &> /dev/null
        	if [ $? -eq 0 ];
        	then
        		echo "用户 $Uname 确认存在"
        		userdel $Uname &> /dev/null
        		id $Uname &> /dev/null
        		if [ ! $? -eq 0 ];
        		then
        			echo "--删除 $Uname 用户成功"
        		else
        			echo "--删除 $Uname 用户失败"
        		fi
        	else
        		echo "用户 $Uname 不存在"
        	fi
        done
        
        [root@linuxprobe test]#
        [root@linuxprobe test]# bash Deluser.sh 
        请输入要删除的用户列表文件：users.txt
        用户 andy 确认存在
        --删除 andy 用户成功
        用户 barry 确认存在
        --删除 barry 用户成功
        用户 carl 确认存在
        --删除 carl 用户成功
        用户 duke 确认存在
        --删除 duke 用户成功
        用户 eric 确认存在
        --删除 eric 用户成功
        用户 george 确认存在
        --删除 george 用户成功
        [root@linuxprobe test]# grep /bin/bash /etc/passwd
        root:x:0:0:root:/root:/bin/bash
        mooc:x:1000:1000:mooc:/home/mooc:/bin/bash
        linuxprobe:x:1001:1001::/home/linuxprobe:/bin/bash
        [root@linuxprobe test]#
    
    从文件中获取ip，检测主机活性
        [root@linuxprobe test]# cat ipadds.txt 
        192.168.10.10
        192.168.10.51
        192.168.10.52
        192.168.10.53
        192.168.10.1
        192.168.10.15
        [root@linuxprobe test]# cat chkhosts.sh 
        #!/bin/bash
        Hlist=$(cat ipadds.txt)
        for IP in $Hlist
        do
        	ping -c 3 -i 0.2 -W 3 $IP &> /dev/null
        	if [ $? -eq 0 ];
        	then
        		echo "Host $IP is On-line."
        	else
        		echo "Host $IP is off-line."
        	fi
        done
        [root@linuxprobe test]# bash chkhosts.sh 
        Host 192.168.10.10 is off-line.
        Host 192.168.10.51 is off-line.
        Host 192.168.10.52 is On-line.
        Host 192.168.10.53 is off-line.
        Host 192.168.10.1 is On-line.
        Host 192.168.10.15 is off-line.
        [root@linuxprobe test]#
    
0015.shell测试语句之while
    $RANDOM变量随机数值的范围为"0~32767"
    猜价格的例子		# 注意该脚本只能接受数字
        [root@linuxprobe test]# cat Guess.sh 
        #!/bin/bash
        PRICE=$(expr $RANDOM % 1000)		# 使用$RANDOM生成随机数并对1000进行取余操作；最后用expr命令取得结果
        TIMES=0
        echo "商品实际价格为0-999之间，猜猜看是多少？"
        while true
        do
        	read -p "请输入你猜测的价格数目：" INT
        	let TIMES++
        	if [ $INT -eq $PRICE ];
        	then
        		echo "恭喜你猜对了，实际价格就是$PRICE"
        		echo "你总共猜测了$TIMES次"
        		exit 0
        	elif [ $INT -gt $PRICE ];
        	then
        		echo "太高了"
        	else
        		echo "太低了"
        	fi
        done
        [root@linuxprobe test]# 
    
0016.shell条件测试语句case
    case 语句是在多个范围内匹配数据，若匹配成功则执行相关命令并结束整个条件测试；而如果数据不在所列出的范围内，则会去执行星号（*）中所定义的默认命令。 
        [root@linuxprobe test]# cat Checkkeys.sh 
        #/bin/bash
        read -p "请输入一个字符，并按Enter键确认：" USER_KEY
        
        case "$USER_KEY" in
        [a-z] | [A-Z])
        	echo "你输入的是字母"
        	;;
        [0-9])
        	echo "你输入的是数字"
        	;;
        *)
        	echo "你输入的是空格、功能键或其它控制字符"
        esac
        [root@linuxprobe test]#
        
0017.计划任务
    一次性计划任务
    	创建计划：at + 时间			# 输入完命令后，按Ctrl+D来结束任务计划编写
    	查看计划：at -l
    	查看计划详细信息：at -c 任务序号
    	删除计划：atrm + 任务序号
    	非交互方式创建计划：echo "命令" | at + 时间			# 例如：echo "systemctl restart httpd" | at 9:00
    
    周期性计划任务			
        创建计划：crontab -e			# 分(0~59) 时(0~23) 日(1~31) 月(1~12) 星期(0-7，0与7皆为周日) 命令
        	# 例如：在每周一、三、五的凌晨3点25分，将网站的数据目录进行打包备份到/media/目录下
        	  25 3 * * 1,3,5 /usr/bin/tar -czvf /media/backup.tar.gz /home/wwwroot	
        查看计划：crontab -l
        删除计划：crontab -r
        编辑他人的计划：crontab -u	用户名	+ 希望进行的操作	# 仅root有权限哦
    
    周期性计划任务说明
    说明1：在编写计划任务的过程中，用逗号(,)来分别表示多个时间段；用减号(-)来表示一段连续的时间周期(如字段"日"的取值为"12-15"，则表示每月的12~15日)；用除号(/)表示执行任务的间隔时间(如分字段"分"的取值为"/2"，则表示每隔2分钟执行一次任务)
    说明2：在crond服务的计划任务参数中，所有命令都一定要用绝对路径的方式来写(whereis 查询命令路径)
    	例如：每周一至周五的凌晨1点钟自动清空/tmp/目录内的所有文件
    		0 1 * * 1-5 /usr/bin/rm -rf /tmp/*
    说明3：计划任务中的"分"字段必须有数值，绝对不能为空或是*号，而"日"和"星期"字段不能同时使用，否则就会发生冲突
    
0018.身份与权限
    文件访问控制列表(Access Control List, ACL)，能让单一用户、用户组对单一文件或目录进行特殊权限设置，让文件仅具有能满足工作需求的最小权限
    
    用户管理
    创建用户：useradd [选项] 用户名
    #参数：
    	-d		# 指定用户的家目录(默认为/home/用户名)
    	-e		# 账户的到期时间(格式：YYY-MM-DD)
    	-u		# 指定用户默认的UID(普通用户的UID从1000开始)
    	-g		# 指定初始的用户基本组(需要是已经存在的组哦)
    	-G		# 指定一个或多个扩展用户组
    	-N		# 不创建与用户同名的基本组
    	-s		# 指定该用户的默认Shell解释器
    
    创建用户组
    命令格式：groupadd 用户组名
    
    修改用户属性
    (查看用户属性：id 用户名)
    命令格式：usermod [选项] 用户名
    	说明：该命令实际修改的是 /etc/passwd 文件中的内容
    usermod命令参数：
    	-C		# 填写用户账户的备注信息
    	-d -m	# 参数 -d 与 -m 连用，可重新指定用户的家目录并自动把旧的数据转移过去
    	-e		# 修改账户的到期时间，格式为 YYYY-MM-DD
    	-g		# 变更所属用户组
    	-G		# 变更扩展用户组
    	    # 例如: usermod -G root linuxprobe, 将用户linuxprobe加入到root用户组中
    	-L		# 锁定用户禁止其登录系统
    	-U		# 解锁用户，允许其登录系统
    	-s		# 变更默认终端
    	-u		# 修改用户的UID
    	    # 例如：usermod -u 1002 linuxprobe, 将用户linuxprobe的UID值修改为1002
    
    passwd命令
    功能：passwd命令用于修改用户密码、过期时间、认证信息等
    命令格式：passwd  [参数]  [用户名]
    passwd参数：
        -l          # 锁定用户，禁止其登录
        -u          # 解除锁定，允许用户登录
        --stdin     # 允许通过标准输入修改用户密码
            # 例如：echo "新密码" | passwd --stdin 用户名
        -d          # 使该用户可用空密码登录系统
        -e          # 强制用户在下次登录时修改密码
        -S          # 显示用户的密码是否被锁定，以及密码所采用的加密算法名称
    
    userdel命令
    功能：userdel命令用于删除用户
    命令格式：userdel  [参数]  用户名
    userdel参数：
        -f          # 强制删除用户
        -r          # 同时删除用户及用户家目录
    
0019.文件权限与归属
    Linux常见文件类型	
    	-：普通文件。
    	d：目录文件。
    	l：链接文件。
    	b：块设备文件。
    	c：字符设备文件。
    	p：管道文件。	
    
    文件权限的字符与数字表示：
    	权限分配	文件所有者		文件所属组       其他用户
    	权限项		读	写	执行	读	写	执行	 读	 写	 执行
    	字符表示	r	w	x		r	w	x		r	w	x
    	数字表示	4	2	1		4	2	1		4	2	1	
    
    权限对于文件
    	可读(r)：表示能够读取文件的实际内容
    	可写(w)：表示能够编辑、新增、修改、删除文件的实际内容
    	可执行(x)：表示能够运行一个脚本程序
    权限对于目录：
    	可读(r)：表示能够读取目录内的文件列表
    	可写(w)：表示能够在目录内新增、删除、重命名文件
    	可执行(x)：表示能够进入该目录
    
    例如：
    显示
    	[root@localhost ~]# ls -l initial-setup-ks.cfg
    	-rw-r--r--. 1 root root 1378 Oct  2 14:03 initial-setup-ks.cfg
    	[root@localhost ~]#
    说明：
    	[root@localhost ~]# ls -l initial-setup-ks.cfg
    	-(文件类型)rw-r--r--(访问权限).(带有SELinux的安全上下文) 1(文件的引用数) root(属主) root(属组)) 1378(占用的磁盘大小) Oct  2 14:03(修改时间) initial-setup-ks.cfg(文件名)
    
0020.文件的特殊权限
    SUID权限
    功能：SUID是一种对二进制程序进行设置的特殊权限，可以让二进制程序的执行者临时拥有属主的权限(仅对拥有执行权限的二进制程序有效)
    典型案例：passwd命令对/etc/shadow文件的修改
    	[root@localhost ~]# ls -l /etc/shadow
    	----------. 1 root root 1141 Oct  2 22:01 /etc/shadow
    	[root@localhost ~]# ls -l /bin/passwd
    	-rwsr-xr-x. 1 root root 27832 Jan 30  2014 /bin/passwd		# 注意属主权限位的s
    	[root@localhost ~]#
    	(如果原本的权限位为 rw- ，即没有 x 执行权限，那么被赋予特殊权限后将变成大写的S)
    
    SGID权限
    功能：
    	* 让执行者临时拥有属组的权限(对拥有执行权限的二进制程序进行设置)
    	* 在某个目录中创建的文件自动继承该目录的用户组(只可以对目录进行设置)
    应用举例：
    	# 在一个部门内设置共享目录，让部门内的所有人员都能够读取目录中的内容，那么就可以创建部门共享目录后，在该目录上设置SGID特殊权限位。这样，部门内的任何人员在里面创建的任何文件都会归属于该目录的所属组，而不再是自己的基本用户组。
    	[root@LinuxProbe test]# mkdir testdir
    	[root@LinuxProbe test]# ls -ald testdir/
    	drwxr-xr-x. 2 root root 6 Oct  3 21:21 testdir/
    	[root@LinuxProbe test]# chmod -Rf 777 testdir/		# 确保普通用户可以向其中写入文件；-R表示递归操作
    	[root@LinuxProbe test]# chmod -Rf g+s testdir/		# 设置SGID权限
    	[root@LinuxProbe test]# ls -ald testdir/
    	drwxrwsrwx. 2 root root 6 Oct  3 21:21 testdir/		# 注意权限位中属组的s
    	[root@LinuxProbe test]# su - linuxprobe				# 切换用户
    	Last login: Wed Oct  3 20:27:54 CST 2018 on pts/1
    	[linuxprobe@LinuxProbe ~]$ cd /tmp/test/testdir/
    	[linuxprobe@LinuxProbe testdir]$ touch testfile.txt
    	[linuxprobe@LinuxProbe testdir]$ ls -al testfile.txt
    	-rw-rw-r--. 1 linuxprobe root 0 Oct  3 21:25 testfile.txt		# 注意属组的名字
    	[linuxprobe@LinuxProbe testdir]$
    
    SBIT权限
    功能：
    	* SBIT权限位可以确保用户只能删除自己的文件，而不能删除其他用户的文件
    	* 当对某个目录设置了SBIT权限后，那么该目录中的文件就只能被其所有者执行删除操作了
    	* 当目录被设置为SBIT权限后，文件的其他人权限部分的x执行权限就会被替换成t或者T
    		# 原本有x执行权限则会写成t，原本没有x执行权限则会被写成T
    	* RHEL 7 系统中的 /tmp/ 目录作为一个共享文件的目录，默认已经设置了SBIT权限(因此除非该目录的所有者，否则无法删除这里面的文件)
    应用举例：
    	[linuxprobe@LinuxProbe ~]$ mkdir linux
    	[linuxprobe@LinuxProbe ~]$ ls -ld linux/
    	drwxrwxr-x. 2 linuxprobe linuxprobe 6 Oct  3 22:50 linux/
    	[linuxprobe@LinuxProbe ~]$ chmod -R o+t linux/			# 添加SBIT权限
    	[linuxprobe@LinuxProbe ~]$ ls -ld linux/
    	drwxrwxr-t. 2 linuxprobe linuxprobe 6 Oct  3 22:50 linux/
    	[linuxprobe@LinuxProbe ~]$		
    效果举例：
    	[root@LinuxProbe tmp]# su - linuxprobe
    	Last login: Wed Oct  3 21:24:52 CST 2018 on pts/0
    	[linuxprobe@LinuxProbe ~]$ ls -ald /tmp/
    	drwxrwxrwt. 15 root root 4096 Oct  3 22:38 /tmp/		# 注意其他人权限位中的t
    	[linuxprobe@LinuxProbe ~]$ cd /tmp
    	[linuxprobe@LinuxProbe tmp]$ touch testfile.txt
    	[linuxprobe@LinuxProbe tmp]$ chmod 777 testfile.txt		# 赋予其最高权限
    	[linuxprobe@LinuxProbe tmp]$ ls -al testfile.txt
    	-rwxrwxrwx. 1 linuxprobe linuxprobe 0 Oct  3 22:38 testfile.txt
    	[linuxprobe@LinuxProbe tmp]$ su - testuser
    	Password:
    	[testuser@LinuxProbe ~]$ rm -rf /tmp/testfile.txt		# 删除被拒绝了
    	rm: cannot remove ‘/tmp/testfile.txt’: Operation not permitted
    	[testuser@LinuxProbe ~]$
    	[testuser@LinuxProbe ~]$ exit		# 再次回到linuxprobe用户
    	logout
    	[linuxprobe@LinuxProbe tmp]$ rm -rf /tmp/testfile.txt		# 正常删除	
    	[linuxprobe@LinuxProbe tmp]$						
    
    (文件能否被删除并不取决于自身的权限，而是看其所在的目录是否有写入权限)		
    
0021.文件的隐藏权限
     文件的隐藏权限能造成的现象如:明明权限充足但却无法删除某个文件的情况，或者仅能在日志文件中追加内容而不能修改或删除内容，这在一定程度上阻止了黑客篡改系统日志的图谋
     
     chattr命令
     功能:设置文件的隐藏权限
     格式:chattr  [+/-参数]  文件
     参数:
    	i		# 无法对文件进行修改;若对目录设置了该参数,则仅能修改其中的子文件内容,而不能新建或删除文件
    	a		# 仅允许追加内容,无法覆盖/删除内容(Append Only,也无法删除文件和修改文件名哦)
    	S		# 文件内容在变更后立即同步到硬盘(sync)
    	s 		# 彻底从硬盘中删除,不可恢复(用0填充原文件所在硬盘区域)
    	A 		# 不再修改这个文件或目录的最后访问时间(atime)如何让用户登录Linux时直接进入某个目录
    	b 		# 不再修改文件的存取时间
    	D 		# 检查压缩文件中的错误
    	d 		# 使用 dump 命令备份时忽略本文件/目录
    	c		# 默认将文件或目录进行压缩
    	u		# 当删除该文件后依然保留其在硬盘中的数据,方便日后恢复
    	t		# 让文件系统支持尾部合并(tail-merging)
    	X 		# 可以直接访问压缩文件中的内容
    功能举例:
    	[root@localhost test]# echo "for Test" > linuxprobe
    	[root@localhost test]# chattr +a  linuxprobe 
    	[root@localhost test]# ls
    	linuxprobe
    	[root@localhost test]# rm -rf linuxprobe 
    	rm: 无法删除"linuxprobe": 不允许的操作
    	[root@localhost test]# echo "test2" > linuxprobe 
    	bash: linuxprobe: 不允许的操作
    	[root@localhost test]# echo "test3" >> linuxprobe 
    	[root@localhost test]# cat linuxprobe 
    	for Test
    	test3
    	[root@localhost test]#
    
    lsattr命令
    功能:显示文件的隐藏权限
    格式:lsattr  [参数]  文件		# 普通使用时不用带参数;-d是查看目录的隐藏权限
    功能举例:
    	[root@localhost test]# ls -al linuxprobe 
    	-rw-r--r--. 1 root root 15 10月  7 10:52 linuxprobe
    	[root@localhost test]# lsattr linuxprobe 
    	-----a---------- linuxprobe
    	[root@localhost test]# rm -rf linuxprobe 
    	rm: 无法删除"linuxprobe": 不允许的操作
    	[root@localhost test]# chattr -a linuxprobe 
    	[root@localhost test]# lsattr linuxprobe 
    	---------------- linuxprobe
    	[root@localhost test]# ls -al linuxprobe 
    	-rw-r--r--. 1 root root 15 10月  7 10:52 linuxprobe
    	[root@localhost test]# rm linuxprobe 
    	rm：是否删除普通文件 "linuxprobe"？y
    	[root@localhost test]#
    
0022.文件访问控制列表(ACL)
    说明1:一般权限,特殊权限和隐藏权限设计的目的都是针对某一类用户设置的;如果希望对某个指定的用户进行单独的权限控制,就需要使用到文件的访问控制列表(ACL)
    说明2:基于普通文件或目录设置ACL,其实就是针对指定的用户或用户组设置文件或目录的操作权限
    说明3:针对目录设置ACL,则目录中的文件会继承其ACL;若单独对文件设置了ACL,则文件不再继承其所在目录的ACL
    
    setfacl命令
    功能:setfacl命令用于管理文件的ACL规则,ACL提供的是 所有者\所属组\其他人 的 读\写\执行 权限之外的特殊权限控制
    格式:setfacl  [参数]  文件
    功能参数:
    	-R		# 针对目录文件使用的递归参数
    	-m		# 针对普通文件使用
    	-b		# 删除某个文件的ACL
    功能举例:
    	[root@localhost ~]# lsattr -d /root
    	---------------- /root
    	[root@localhost ~]# ls -ld /root
    	dr-xr-x---. 15 root root 4096 10月  7 16:03 /root
    	[root@localhost ~]# setfacl -Rm u:linuxprobe:rwx /root		# 修改/root目录的ACL权限
    	[root@localhost ~]# ls -ld /root		# 注意权限位的变化(权限位最后的点.变成了+号)
    	dr-xrwx---+ 15 root root 4096 10月  7 16:03 /root
    	[root@localhost ~]# lsattr -d /root
    	---------------- /root
    	[root@localhost ~]# su - linuxprobe
    	上一次登录：日 10月  7 11:12:24 CST 2018pts/1 上
    	[linuxprobe@localhost ~]$ cd /root		# 普通用户能够进入/root/目录了
    	[linuxprobe@localhost root]$ ls			# 查看该目录下的内容
    	anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Pictures  Templates  Videos
    	Desktop          Downloads  Music                 Public    test
    	[linuxprobe@localhost root]$ pwd
    	/root
    	[linuxprobe@localhost root]$ touch test.txt		# 尝试新建文件成功
    	[linuxprobe@localhost root]$ ls
    	anaconda-ks.cfg  Documents  initial-setup-ks.cfg  Pictures  Templates  test.txt
    	Desktop          Downloads  Music                 Public    test       Videos
    	[linuxprobe@localhost root]$ exit
    	logout
    	[root@localhost ~]# 
    
    getfacl命令
    功能:getfacl命令用于显示文件上设置的ACL信息
    格式:getfacl 文件名称
    功能举例:
    	[root@localhost ~]# getfacl  /root
    	getfacl: Removing leading '/' from absolute path names
    	# file: root
    	# owner: root
    	# group: root
    	user::r-x
    	user:linuxprobe:rwx				# 这是之前添加进来的ACL规则
    	group::r-x
    	mask::rwx
    	other::---
    
    	[root@localhost ~]# 
	
0023.su命令与sudo服务
    sudo命令
    功能:
    	* 限制用户执行指定的命令
    	* 记录用户执行的每一条命令
    	* 配置文件(/etc/sudoers)提供集中的用户管理,权限与主机等参数
    	* 验证密码的后5分钟内(默认值)无须再让用户再次验证密码
    
    命令参数：
    	-h                 # 列出帮助信息
    	-l                 # 列出当前用户可执行的命令
    	-u 用户名或UID值    # 以指定的用户身份执行命令
    	-k                 # 清空密码的有效时间，下次执行执行sudo时需要再次进行密码验证
    	-b                 # 在后台执行指定的命令
    	-p                 # 更改询问密码的提示语
    
    命令修改:
    	* root用户可以使用 visudo 命令编辑sudo服务的配置文件
        	   # 优点是可以对配置文件内的参数进行语法检查,并在发现参数错误时进行报错
        	   在第99行填写类似指定的信息:linuxprobe      ALL=(ALL)       ALL
        	       # 说明: 用户名	允许使用的主机=(以哪个用户的身份)  可执行的命令列表
    功能举例:
    	* 让某个用户能使用root管理员的身份执行指定的命令,配置时需要给出该命令的绝对路劲(否则无法识别)
    	[root@localhost ~]# visudo	
    		97 ## Allow root to run any commands anywhere
    		98 root    ALL=(ALL)       ALL
    		99 linuxprobe      ALL=(ALL)       /usr/bin/cat
    			# 说明：如果此处将 ALL=(ALL) 修改为 ALL=NOPASSWD ,则该用户在使用sudo命令时就不需要输入密码了
    	[root@localhost ~]# su - linuxprobe
    	上一次登录：日 10月 21 11:55:08 CST 2018pts/4 上
    	[linuxprobe@localhost ~]$ cat /etc/shadow | grep linuxprobe
    	cat: /etc/shadow: Permission denied								# 访问被拒绝
    	[linuxprobe@localhost ~]$ sudo cat /etc/shadow | grep linuxprobe
    	[sudo] password for linuxprobe: 								# 输入自己的密码后访问成功
    	linuxprobe:$6$BBByE/0wZC7K98e1$n2D4P8JA6xe5H0KHD5i6FKBqFNFflvH5XF4ARuzEgxIJKn.yQfsKGq4ZkrzCfJfw4K/ASKsuNi90f3GNDWkqg1:17809:0:99999:7:::
    	[linuxprobe@localhost ~]$ 

0024.存储结构与磁盘划分
    Linux系统常见目录
        /boot       # 开机所需文件---内核、启动选择菜单以及所需的配置文件等
        /dev        # 以文件形式存放任何设备与接口
        /etc        # 配置文件
        /home       # 用户家目录
        /bin        # 存放单用户模式下还可以操作的命令
        /lib        # 开机时用到的函数库，以及/bin/与/sbin/下面命令要调用的函数
        /sbin       # 开机过程中需要的命令
        /media      # 用于挂在设备文件的目录
        /opt        # 放置第三方的软件
        /root       # 系统管理员的家目录
        /srv        # 一些网络服务的数据文件目录
        /tmp        # 任何人均可使用的"共享"临时目录
        /proc       # 虚拟文件系统；例如系统内核、进程、外部设备及网络状态等
        /usr/local  # 用户自行安装的软件
        /usr/sbin   # Linux系统开机时不会使用到的软件/命令/脚本
        /usr/share  # 帮助与说明文件，也可以放置共享文件
        /var        # 主要存放经常变化的文件，如日志
        /lost+found # 当文件系统发生错误时，将一些丢失的文件片段存放在这里
    
    /dev目录下的的设备文件会由udev设备管理器以守护进程的形式侦听内核信号来管理
    Linux系统中常见硬件设备名称        # udev设备管理器服务决定了设备在/dev/目录中的名称
                IDE设备               # /dev/hd[a-d]
        SCSI/SATA/U盘        # /dev/sd[a-p]        #这里的顺序是由系统内核的识别顺序来决定的
        软驱                 # /dev/fd[0-1]
        打印机               # /dev/lp[0-15]
        光驱                 # /dev/sr0 或 /dev/cdrom
        鼠标                 # /dev/mouse
        磁带机               # /dev/st0 或 /dev/ht0IDE设备           # /dev/hd[a-d]
        SCSI/SATA/U盘    # /dev/sd[a-p]        #这里的顺序是由系统内核的识别顺序来决定的
        软驱              # /dev/fd[0-1]
        打印机             # /dev/lp[0-15]
        光驱              # /dev/sr0 或 /dev/cdrom
        鼠标              # /dev/mouse
        磁带机             # /dev/st0 或 /dev/ht0
    
    硬盘的分区编号：
        * 主分区或扩展分区的编号从1开始，到4结束
        * 逻辑分区从编号5开始
        
    硬盘设备：
        * 由大量的扇区组成，每个扇区的容量为512字节
        * 第一个扇区非常重要，里面保存着主引导记录和分区表信息
            --主引导记录需要占用446字节
            --分区表占用64字节
            --结束符占用2字节
        * 分区表中每记录一个分区信息需要16字节
            --这意味着最多只有4个分区可以写到第一扇区中
            --这4个分区就是4个主分区
        * 解决分区个数不够的问题
            --从第一扇区的分区表中取出16个字节，用来指向另外一个分区，建立扩展分区
            --在扩展分区中，用户可以创建出多个逻辑分区(可以大于4个)
    
    文件系统：
        * ext4文件系统
            --是一种日志文件系统，ext3的改进版本，支持的存储容量为1EB
                # EB全称为Exabyte，中文名为:艾字节
                # 1EB = 1024PB
                # PB全称为Petabyte，中文名为:拍字节
                # 1PB = 1024TB
                # TB全称为Terabyte，中文名为：太字节
                # 1TB = 1024GB
                # GB全称为Gigabyte，中文名为：吉字节(也称千兆)
            --能够由无限多的子目录
            --能极大的提高(相对于ext3)读写效率：批量分配block块
        * XFS文件系统
            --是一种高性能的日志文件系统，可在意外宕机后快速恢复可能被破坏的文件
            --它强大的日志功能，只需要花费极低的计算和存储性能
            --最大支持存储容量为18EB(这是最主要的功能)
        
    文件存储：
    Linux将每个文件的权限与属性记录在inode中，并且每个文件占用一个独立的inode表格
    inode表格的默认大小为128B(字节)，记录的信息如下
        --该文件的访问权限(read,write,execute)
        --该文件的所有者和所属组(owner、group)
        --该文件的大小(size)
        --该文件的创建或内容修改时间(ctime)
        --该文件的最后一次访问时间(atime)
        --该文件的修改时间(mtime)
        --文件的特殊权限(SUID、SGID、SBIT)
        --该文件的真实数据地址(point)
    一个inode的默认大小为128B或256B(ext3/ext4)，记录一个block则消耗4B，当文件的一个inode被写满后，系统会自动分配一个新的block块，用于像inode那样记录其它block块的信息，这样将各个block块的内容串到一起，就能够让用户读到完整的文件内容了
    Linux文件的内容保存在block块中(可以是1KB、2KB、或4KB)
    对于存储文件内容的block块，由两种情况(以4KB的block块为例):
        --情况1:文件很小(1KB),但依然会占用一个block，因此会潜在地浪费3KB
        --情况2：文件很大(5KB)，那么会占用两个block
    
    设备挂载--mount命令
    mount命令参数
        -a      # 挂载所有在/etc/fstab文件中定义的文件系统
        -t      # 指定文件系统类型(目前较新的Linux会自动判断)
    
    /etc/fstab文件的挂载信息格式
        --设备文件  挂载目录    格式类型    权限类型    是否备份    是否自检
        说明：
        -设备文件       # 一般为设备的路径+设备名称,也可以写唯一识别码(UUID)
        -挂载目录       # 指定要挂载到的目录；需要在挂载前创建好
        -格式类型       # 指定文件系统的格式，如ext3、ext4、xfs、swap、iso9660(此为光盘设备)等
        -权限选项       # 若设置为defaults,则默认权限为：rw,suid,dev,exec,auto,nouser,async
        -是否备份       # 若为1则开机后使用dump进行磁盘备份，为0则不备份
        -是否自检       # 若为1则开机后自动进行磁盘自检，为0则不自检
        例如：/dev/cdrom   /media/cdrom    iso9660 defaults    0   0
    
    设备卸载--umount命令
        格式：umount  挂载点/设备文件
        例如：umount /dev/sdb2
    
    分区与磁盘管理
    fdisk命令
    格式：fdisk 磁盘设备文件
        # 如：fdisk /dev/sdb      
    操作参数：
        m       # 查看全部可用的参数
        n       # 添加新的分区
                  --添加分区时，输入p创建主分区，输入e创建扩展分区
                  --当输入e创建扩展分区后，输入l可创建逻辑分区
                  --创建分区时:起始扇区直接保持默认，结束扇区输入我们需要的磁盘容量，如:+10GB
        d       # 删除某个分区信息
        l       # 是L哦；列出所有可用的分区类型
        t       # 改变某个分区的类型
        p       # 查看分区信息
        w       # 保存并退出
        q       # 不保存直接退出
     说明：以上操作完成后，系统会自动将分区信息同步给内核;如果没有自动同步的话，可通过命令：partprobe，手动将分区信息同步到内核(该命令可以多次使用)；如果还是未同步的话，可以考虑重启系统哦
     
     分区的格式化--mkfs命令
     格式：mksf.文件系统名称  设备文件
     常用文件系统名称： 
        [root@localhost ~]# mkfs        # 按两下tab键会出现提示
        mkfs         mkfs     [root@localhost ~]# free -h
                  total       used       free     shared    buffers     cached
        Mem:          723M       333M       390M       5.4M       1.1M       111M
        -/+ buffers/cache:       220M       503M
        Swap:         1.5G         0B       1.5G
        [root@localhost ~]# mkswap /dev/sdb1       # 将分区格式化为swap分区
        正在设置交换空间版本 1，大小 = 5242876 KiB
        无标签，UUID=7c463a88-a856-4621-af3f-53408893522b  
        [root@localhost ~]# swapon /dev/sdb1       # 挂载swap分区
        [root@localhost ~]# free -h
                  total       used       free     shared    buffers     cached
        Mem:          723M       337M       386M       5.4M       1.1M       111M
        -/+ buffers/cache:       224M       499M
        Swap:         6.5G         0B       6.5G
        [root@localhost ~]#.cramfs  mkfs.ext3    mkfs.fat     mkfs.msdos   mkfs.xfs
        mkfs.btrfs   mkfs.ext2    mkfs.ext4    mkfs.minix   mkfs.vfat    
     例子：   
        [root@localhost ~]# mkfs.ext4 /dev/sdb1
     
     交换分区--SWAP分区
     功能：将内存中暂时不用的数据临时存放到硬盘中，其存在的目的是为了解决物理内存不足的问题；且只有当物理内存耗尽后才会调用交换分区的资源;在生产环境中，交换分区一般为真实物理内存的1.5~2倍
     格式化为swap分区命令：mkswap
        # 例如：mkswap /dev/sdb1           # /dev/sdb1是用fdisk正常分出来的普通分区
     挂载swap分区命令：swapon
     例子：
        [root@localhost ~]# free -h
                  total       used       free     shared    buffers     cached
        Mem:          723M       333M       390M       5.4M       1.1M       111M
        -/+ buffers/cache:       220M       503M
        Swap:         1.5G         0B       1.5G
        [root@localhost ~]# mkswap /dev/sdb1       # 将分区格式化为swap分区
        正在设置交换空间版本 1，大小 = 5242876 KiB
        无标签，UUID=7c463a88-a856-4621-af3f-53408893522b  
        [root@localhost ~]# swapon /dev/sdb1       # 挂载swap分区
        [root@localhost ~]# free -h
                  total       used       free     shared    buffers     cached
        Mem:          723M       337M       386M       5.4M       1.1M       111M
        -/+ buffers/cache:       224M       499M
        Swap:         6.5G         0B       6.5G
        [root@localhost ~]#
     注意：如果需要重启系统后依然生效，需要将挂载信息写入/etc/fstab文件中
     写入格式：/dev/sdb2      swap      swap      defaults  0    0
    
    磁盘容量配额--quota
    quota命令可以限制用户可以使用的硬盘容量和所能创建的文件个数
    quota命令不同的限制功能：
      --软限制：当达到软限制时会提示用户，但仍允许用户在限定的额度内继续使用
      --硬限制：当达到硬限制时会提示用户，且强制终止用户的操作
    说明：存储设备默认情况下是没有开启对quota支持的(但软件是安装了的哦)，需要手动编辑配置文件,配置是在/etc/fstab文件中进行配置的
    例子：
        [root@localhost ~]# mount | grep boot       # 注意下面的noquota,表示该分区不支持quota磁盘配额技术
        /dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
        [root@localhost ~]#
        [root@localhost ~]# cat /etc/fstab          # 在defaults后加上uquota参数
        /dev/mapper/rhel-root   /                       xfs     defaults        1 1
        UUID=d5012868-1809-466c-a261-65c01db54eba /boot                   xfs     defaults,uquota        1 2
        /dev/mapper/rhel-swap   swap                    swap    defaults        0 0
        [root@localhost ~]# reboot                    # 修改完成，重启系统
        [root@localhost ~]# mount | grep boot       # 注意最后，该分区已经支持quota磁盘配额技术了
        /dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,usrquota)
        [root@localhost ~]# 
    (说明：redhat6及之前的版本使用的参数是usrquota，redhat7及之后的版本使用的参数是uquota)
    
    xfs_quota命令
    说明：xfs_quota命令是一个专门针对XFS文件系统管理quota磁盘容量配额服务而设计的命令
    命令格式：xfs_quota [参数] 配额 文件系统
    参数：
        -c      # 用于以参数的形式设置要执行的命令
        -x      # 专家模式；能够对quota服务进行更多复杂的配置
    例子：
        [root@localhost ~]# mount | grep boot       # 确认/boot/分区已经开启quota磁盘配额技术
            /dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,usrquota)
        [root@localhost ~]# useradd tom     # 创建一个用户tom，用于测试效果
        [root@localhost ~]# chmod -Rf o+w /boot     # 修改/boot/目录的权限，让普通用户拥有该目录的写入权限
        [root@localhost ~]# xfs_quota -x -c "limit bsoft=3m bhard=6m isoft=3 ihard=6 tom" /boot/
        # 对用户tom进行磁盘配额，软限制为3m，硬限制为6m
        [root@localhost ~]# xfs_quota -x -c report /boot/       # 打印磁盘配额使用情况
            User quota on /boot (/dev/sda1)
                                           Blocks                     
            User ID          Used       Soft       Hard    Warn/Grace     
            ---------- -------------------------------------------------- 
            root            95388          0          0     00 [--------]
            tom                 0       3072       6144     00 [--------]  
        [root@localhost ~]# 
        [root@localhost ~]# su tom
        [tom@localhost root]$ dd if=/dev/zero of=/boot/tom.txt bs=5M count=1
            记录了1+0 的读入
            记录了1+0 的写出
            5242880字节(5.2 MB)已复制，0.0144819 秒，362 MB/秒
        [tom@localhost root]$       
        [root@localhost ~]# xfs_quota -x -c report /boot/       # 再次查看磁盘配额
            User quota on /boot (/dev/sda1)
                                           Blocks                     
            User ID          Used       Soft       Hard    Warn/Grace     
            ---------- -------------------------------------------------- 
            root            95388          0          0     00 [--------]
            tom              5120       3072       6144     00  [6 days]
        [root@localhost ~]# su - tom
            上一次登录：日 12月  2 11:55:31 CST 2018pts/0 上
        [tom@localhost ~]$ dd if=/dev/zero of=/boot/tom2.txt bs=8M count=1
            dd: error writing ‘/boot/tom2.txt’: Disk quota exceeded             # 写入报错，提示磁盘配额超标
            1+0 records in
            0+0 records out
            1048576 bytes (1.0 MB) copied, 0.00769123 s, 136 MB/s
        [tom@localhost ~]$ exit
            logout
        [root@localhost ~]# xfs_quota -x -c report /boot/       # 再次查看磁盘配额
            User quota on /boot (/dev/sda1)
                                           Blocks                     
            User ID          Used       Soft       Hard    Warn/Grace     
            ---------- -------------------------------------------------- 
            root            95388          0          0     00 [--------]
            tom              6144       3072       6144     00  [6 days]
        [root@localhost ~]#

    edquota命令
    说明：edquota命令用于编辑用户的quota配额限制；equota命令会调用vi或vim来让root修改要限制的具体细节
    格式：edquota [参数] [用户]
    参数：
        -u      # 指定用户进行设置
        -g      # 指定某个用户组进行设置
    例子：在开始之前先将前面在/boot/目录下创建的所有tom文件删掉
        [root@localhost ~]# edquota -u tom          # 将之前的限额修改为8M
        Disk quotas for user tom (uid 1001):
          Filesystem                   blocks       soft       hard     inodes     soft     hard
          /dev/sda1                         0       3072       8192          0        3        6
        [root@localhost ~]# xfs_quota -x -c report /boot/
        User quota on /boot (/dev/sda1)
                                       Blocks                     
        User ID          Used       Soft       Hard    Warn/Grace     
        ---------- -------------------------------------------------- 
        root            95388          0          0     00 [--------]
        tom                 0       3072       8192     00 [--------]
        
        [root@localhost ~]# su - tom
        上一次登录：日 12月  2 12:27:15 CST 2018pts/0 上
        [tom@localhost ~]$ dd if=/dev/zero of=/boot/tom1.txt bs=8M count=1
        1+0 records in
        1+0 records out
        8388608 bytes (8.4 MB) copied, 0.00553813 s, 1.5 GB/s
        [tom@localhost ~]$
        [tom@localhost ~]$ dd if=/dev/zero of=/boot/tom2.txt bs=1M count=1
        dd: failed to open ‘/boot/tom2.txt’: Disk quota exceeded
        [tom@localhost ~]$ 

    软硬方式链接
    硬链接(hard link)：
        × 可以将其理解为一个“指向原始文件inode的指针”，系统不为它分配独立的inode和文件
        × 硬链接文件与原始文件其实是同一个文件，只是名字不同
        × 每当给一个文件添加一个硬链接，该文件的inode连接数就会增加1
        × 只有当文件的inode连接数为0时，才算彻底将它删除
        × 硬链接是指向源文件inode的指针，即使删掉原始文件，依然可以通过硬链接文件来访问
        × 硬链接不能跨分区对目录和文件进行链接
    软链接(sysmbolic link):
        × 仅包含所链接文件的路径名
        × 能链接目录文件
        × 可跨文件系统进行链接
        × 当原始文件被删除后，链接文件也将失效

    ln命令
    说明：ln命令用于创建链接文件
    格式：ln [参数] 目标
    参数：
        -s      # 创建软链接(如果不带-s参数，则默认创建硬链接)
        -f      # 强制创建文件或目录的链接
        -i      # 覆盖前先询问
        -v      # 显示创建链接的过程
    例子：
    软链接示例
        [root@localhost tmp]# echo "Welcome to linuxprobe.com" > read1.txt
        [root@localhost tmp]# ln -s read1.txt read2.txt
        [root@localhost tmp]# ls -l read2.txt 
        lrwxrwxrwx. 1 root root 9 12月  2 13:06 read2.txt -> read1.txt       # 系统下此时显示read2.txt颜色为彩色
        [root@localhost tmp]# cat read1.txt 
        Welcome to linuxprobe.com
        [root@localhost tmp]# cat read2.txt 
        Welcome to linuxprobe.com
        [root@localhost tmp]# rm -rf read1.txt 
        [root@localhost tmp]# ls -l read2.txt 
        lrwxrwxrwx. 1 root root 9 12月  2 13:06 read2.txt -> read1.txt
            # 此时，系统下查看时，read2.txt颜色会变红，read1.txt显示会闪烁   
        [root@localhost tmp]# cat read2.txt 
        cat: read2.txt: 没有那个文件或目录
        [root@localhost tmp]# 
    硬链接示例
        [root@localhost tmp]# echo "Welcome to linuxprobe.com" > read1.txt
        [root@localhost tmp]# ln read1.txt read2.txt
        [root@localhost tmp]# cat read1.txt 
        Welcome to linuxprobe.com
        [root@localhost tmp]# cat read2.txt 
        Welcome to linuxprobe.com
        [root@localhost tmp]# ls -l read2.txt 
        -rw-r--r--. 2 root root 26 12月  2 13:13 read2.txt       # 系统下此时显示read2.txt颜色会很鲜艳哦
        [root@localhost tmp]# rm -rf read1.txt 
        [root@localhost tmp]# cat read2.txt 
        Welcome to linuxprobe.com
        [root@localhost tmp]# ls -l read2.txt 
        -rw-r--r--. 1 root root 26 12月  2 13:13 read2.txt       # 系统下此时显示read2.txt颜色很普通
        [root@localhost tmp]# 

0025.RAID与LVM磁盘整列技术
    RAID(Redundant Array of Independent Disk，独立冗余磁盘阵列)
    RAID5
        × 磁盘阵列组中数据的奇偶校验信息是存储到除自身以外的其它每一块硬盘设备中
        × 当某一块硬盘设备出现问题后，可以通过奇偶校验信息来尝试重建损坏的数据
        × 该技术兼顾了硬盘设备的读写速度、数据安全性与存储成本的问题
    
    RAID10
        × 该技术是RAID1与RAID0技术的组合体
        × 该技术至少需要 4 块硬盘才能组建
        × 组建过程：先两两制作成RAID1，保证数据的安全性，然后再对两个RAID1做RAID0，提高设备的读写速度
        × 理论上只要坏的不是同一组中的所有硬盘，那么最多可以损坏50%的硬盘而不丢失数据
        × 在不考虑成本的情况下，该技术在性能上超过了RAID5

    LVM(Logical Volume Manager，逻辑卷管理器)
        × 允许用户对硬盘资源进行动态调整
        × 在硬盘分区和文件系统之间添加了一个逻辑层，并提供了一个抽象的卷组，可以将多块硬盘进行卷组合并
        × 实现了对硬盘分区的动态调整
        × 建立顺序是：物理卷(PV)-->卷组(VG)-->逻辑卷(LV)


    部署磁盘阵列
    mdadm命令
    说明：mdadm命令用于管理Linux系统中的软件RAID硬盘阵列
    格式：mdadm [模式] <RAID设备名称> [参数] [成员设备名称]
    参数：
        -a      * 检查设备名称；加yes则自动创建设备文件；添加新的磁盘设备
        -n      * 指定设备数量
        -l      * 指定RAID级别;是L的小写哦
        -C      * 创建RAID阵列卡
        -v      * 显示过程
        -f      * 模拟设备损坏
        -r      * 移除设备
        -Q      * 查看摘要信息
        -D      * 查看详细信息
        -S      * 停止RAID阵列
        -x      * 备份盘
    命令解析说明：
    nano@GBooter:~$ mdadm -Cv /dev/md0 -a yes -n 4 -l 10 /dev/sdb /dev/sdc /dev/sdd /dev/sde
    解析：-C参数表示创建一个RAID阵列卡；-v参数显示创建的过程，同时在后面追加一个设备名称/dev/md0，也就是创建后RAID磁盘阵列的名称；-a yes参数表示自动创建设备文件；-n 4 参数代表使用4块硬盘来部署这个RAID磁盘阵列；-l 10 参数代表使用RAID 10方案；最后再加上4块硬盘设备的名称
    例子：
        [root@localhost ~]# mdadm -Cv /dev/md0 -a yes -n 4 -l 10 /dev/sdb /dev/sdc /dev/sdd /dev/sde 
            mdadm: layout defaults to n2
            mdadm: layout defaults to n2
            mdadm: chunk size defaults to 512K
            mdadm: /dev/sdb appears to be part of a raid array:
                level=raid0 devices=0 ctime=Thu Jan  1 08:00:00 1970
            mdadm: partition table exists on /dev/sdb but will be lost or
                   meaningless after creating array
            mdadm: size set to 20954624K
            Continue creating array? y
            mdadm: Defaulting to version 1.2 metadata
            mdadm: array /dev/md0 started.
        [root@localhost ~]# mkfs.ext4 /dev/md0          # 将制作好的RAID磁盘阵列格式化为ext4格式
            mke2fs 1.42.9 (28-Dec-2013)
            文件系统标签=
            OS type: Linux
            块大小=4096 (log=2)
            ...格式化输出...
        [root@localhost ~]# mkdir /RAID_md0
        [root@localhost ~]# mount /dev/md0 /RAID_md0/       # 挂载md0设备
        [root@localhost ~]# df -h
            文件系统               容量  已用  可用 已用% 挂载点
            /dev/mapper/rhel-root   38G  3.0G   36G    8% /
            devtmpfs               353M     0  353M    0% /dev
            tmpfs                  362M     0  362M    0% /dev/shm
            tmpfs                  362M  5.4M  357M    2% /run
            tmpfs                  362M     0  362M    0% /sys/fs/cgroup
            /dev/sda1              497M  127M  371M   26% /boot
            /dev/md0                40G   49M   38G    1% /RAID_md0         # 挂载后只有40GB；前面使用的每块盘为20GB
        [root@localhost ~]# mdadm -D /dev/md0           # 查看/dev/md0磁盘阵列的详细信息
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 10:54:40 2018
             Raid Level : raid10
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
           Raid Devices : 4
          Total Devices : 4
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 10:59:01 2018
                  State : clean 
         Active Devices : 4
        Working Devices : 4
         Failed Devices : 0
          Spare Devices : 0
        
                 Layout : near=2
             Chunk Size : 512K
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : 7b7a3a73:60c46a0c:eb5c86f4:63302646
                 Events : 19
        
            Number   Major   Minor   RaidDevice State
               0       8       16        0      active sync   /dev/sdb
               1       8       32        1      active sync   /dev/sdc
               2       8       48        2      active sync   /dev/sdd
               3       8       64        3      active sync   /dev/sde
        [root@localhost ~]# echo "/dev/md0 /RAID_md0 ext4 defaults 0 0" >> /etc/fstab   # 写入配置文件，使其永久生效
        [root@localhost ~]#
        
    磁盘阵列的损坏及修复
    说明：在确认有一块物理硬盘设备出现损坏后，可用mdad命令的 -r 参数将其移除
    例子：
        [root@localhost ~]# mdadm /dev/md0 -f /dev/sdb          # 使用-f参数模拟/dev/sdb设备损坏
          mdadm: set /dev/sdb faulty in /dev/md0
        [root@localhost ~]# mdadm -D /dev/md0           # 查看磁盘阵列的状态
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 10:54:40 2018
             Raid Level : raid10
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
           Raid Devices : 4
          Total Devices : 4
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 11:11:18 2018
                  State : clean, degraded 
         Active Devices : 3
        Working Devices : 3
         Failed Devices : 1
          Spare Devices : 0
        
                 Layout : near=2
             Chunk Size : 512K
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : 7b7a3a73:60c46a0c:eb5c86f4:63302646
                 Events : 21
        
            Number   Major   Minor   RaidDevice State
               0       0        0        0      removed
               1       8       32        1      active sync   /dev/sdc
               2       8       48        2      active sync   /dev/sdd
               3       8       64        3      active sync   /dev/sde
        
               0       8       16        -      faulty   /dev/sdb           # 提示/dev/sdb设备已经出现故障
        [root@localhost ~]# 
        说明：在RAID 10中，一个盘故障并不会影响正个阵列的使用，在此期间，我们可以在/RADI_md0/目录中正常的创建或删除文件；如果此时重启一下系统，可以发现，出现故障的硬盘已经被md0磁盘整列移除了
        [root@localhost ~]# mdadm -D /dev/md0
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 10:54:40 2018
             Raid Level : raid10
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
         [root@localhost ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /deb/sdc /dev/sdd /dev/sde  Raid Devices : 4
          Total Devices : 3
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 11:03:30 2018
                  State : clean, degraded 
         Active Devices : 3
        Working Devices : 3
         Failed Devices : 0
          Spare Devices : 0
        
                 Layout : near=2
             Chunk Size : 512K
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : 7b7a3a73:60c46a0c:eb5c86f4:63302646
         [root@localhost ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /deb/sdc /dev/sdd /dev/sde        Events : 27
        
            Number   Major   Minor   RaidDevice State
               0       0        0        0      removed
               1       8       32        1      active sync   /dev/sdc
               2       8       48        2      active sync   /dev/sdd
               3       8       64        3      active sync   /dev/sde
        [root@localhost ~]#
        说明：下面插入一块新磁盘
        [root@localhost ~]# mdadm /dev/md0 -a /dev/sdf          # 将新插入的硬盘/dev/sdf添加到磁盘阵列/dev/md0中
        mdadm: added /dev/sdf
        [root@localhost ~]# mdadm -D /dev/md0
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 10:54:40 2018
             Raid Level : raid10
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
           Raid Devices : 4
          Total Devices : 4
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 11:33:49 2018
                  State : clean, degraded, recovering 
         Active Devices : 3
        Working Devices : 4
         Failed Devices : 0
          Spare Devices : 1
        
                 Layout : near=2
             Chunk Size : 512K
        
         Rebuild Status : 13% complete
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : 7b7a3a73:60c46a0c:eb5c86f4:63302646
                 Events : 33
        
            Number   Major   Minor   RaidDevice State
               4       8       80        0      spare rebuilding   /dev/sdf         # 刚添加完成，正在重建
               1       8       32        1      active sync   /dev/sdc
               2       8       48        2      active sync   /dev/sdd
               3       8       64        3      active sync   /dev/sde
        [root@localhost ~]# 
        注意：此时的磁盘阵列已经满足了初始创建时的4块硬盘数量，如果再向其中添加新硬盘也不会增加容量的哦
        [root@localhost ~]# mdadm /dev/md0 -a /dev/sdb
            mdadm: added /dev/sdb
        [root@localhost ~]# mdadm -D /dev/md0
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 10:54:40 2018
             Raid Level : raid10
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
           Raid Devices : 4
          Total Devices : 5
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 11:36:16 2018
                  State : clean 
         Active Devices : 4
        Working Devices : 5
         Failed Devices : 0
          Spare Devices : 1
        
                 Layout : near=2
             Chunk Size : 512K
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : 7b7a3a73:60c46a0c:eb5c86f4:63302646
                 Events : 53
        
            Number   Major   Minor   RaidDevice State
               4       8       80        0      active sync   /dev/sdf          # 之前添加完成的盘已经重建完成了
               1       8       32        1      active sync   /dev/sdc
               2       8       48        2      active sync   /dev/sdd
               3       8       64        3      active sync   /dev/sde
        [root@localhost ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /deb/sdc /dev/sdd /dev/sde
               5       8       16        -      spare   /dev/sdb                # 该盘并未直接处于使用状态，而是作为备用盘存在
        [root@localhost ~]#
        [root@localhost ~]# mdadm /dev/md0 -f /dev/sdc                      # 模拟损坏硬盘/dev/sdc
            mdadm: set /dev/sdc faulty in /dev/md0  
        [root@localhost ~]# mdadm -D /dev/md0                               # 再次查看磁盘阵列信息
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 10:54:40 2018
             Raid Level : raid10
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
           Raid Devices : 4
          Total Devices : 5
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 11:43:49 2018
                  State : clean, degraded, recovering 
         [root@localhost ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /deb/sdc /dev/sdd /dev/sdeActive Devices : 3
        Working Devices : 4
         Failed Devices : 1
          Spare Devices : 1
        
                 Layout : near=2
             Chunk Size : 512K
        
         Rebuild Status : 14% complete
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : 7b7a3a73:60c46a0c:eb5c86f4:63302646
                 Events : 59
        
            Number   Major   Minor   RaidDevice State
               4       8       80        0      active sync   /dev/sdf
               5       8       16        1      spare rebuilding   /dev/sdb  # 之前处于备用状态的硬盘/dev/sdb已经立即被加入了阵列中
               2       8       48        2      active sync   /dev/sdd
               3       8       64        3      active sync   /dev/sde
        
               1       8       32        -      faulty   /dev/sdc            # 故障的硬盘/dev/sdc已经被阵列移除了
        [root@localhost ~]# 

    磁盘阵列+备份盘            # 基于RAID5
    命令解析说明：
    [root@localhost ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /dev/sdc /dev/sdd /dev/sde
    解析：-C参数表示创建一个RAID阵列卡；-v参数显示创建的过程，同时在后面追加一个设备名称/dev/md0，也就是创建后RAID磁盘阵列的名称；-n 3 参数代表使用3块硬盘来部署这个RAID磁盘阵列；-l 5 参数代表使用RAID 5方案；-x 1 参数表示有一块备份盘；最后再加上4块硬盘设备的名称
    例子：
        [root@localhost ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /dev/sdc /dev/sdd /dev/sde
            mdadm: layout defaults to left-symmetric
            mdadm: layout defaults to left-symmetric
            mdadm: chunk size defaults to 512K
            mdadm: size set to 20954624K
            mdadm: Defaulting to version 1.2 metadata
            mdadm: array /dev/md0 started.
        [root@localhost ~]# mdadm -D /dev/md0           # 查看磁盘阵列的详细信息
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 17:10:29 2018
             Raid Level : raid5
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
           Raid Devices : 3
          Total Devices : 4
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 17:10:43 2018
                  State : clean, degraded, recovering 
         Active Devices : 2
        Working Devices : 4
         Failed Devices : 0
          Spare Devices : 2
        
                 Layout : left-symmetric
             Chunk Size : 512K
        
         Rebuild Status : 14% complete
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : b68a693d:68b48e22:eafea3ec:7edb7576
                 Events : 3
        
            Number   Major   Minor   RaidDevice State
               0       8       16        0      active sync   /dev/sdb
               1       8       32        1      active sync   /dev/sdc
               4       8       48        2      spare rebuilding   /dev/sdd
        
               3       8       64        -      spare   /dev/sde            # 该盘就是备份盘
        [root@localhost ~]# mkfs.ext4 /dev/md0          # 格式化磁盘阵列为ext4格式
            mke2fs 1.42.9 (28-Dec-2013)
            文件系统标签=
            OS type: Linux
            块大小=4096 (log=2)
            分块大小=4096 (log=2)
            Stride=128 blocks, Stripe width=256 blocks
            2621440 inodes, 10477312 blocks
            523865 blocks (5.00%) reserved for the super user
            第一个数据块=0
            Maximum filesystem blocks=2157969408
            320 block groups
            32768 blocks per group, 32768 fragments per group
            8192 inodes per group
            Superblock backups stored on blocks: 
            	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
            	4096000, 7962624
            
            Allocating group tables: 完成                            
            正在写入inode表: 完成                            
            Creating journal (32768 blocks): 完成
            Writing superblocks and filesystem accounting information: 完成   
            
        [root@localhost ~]# mkdir /RAID_md0             # 创建挂载文件夹
        [root@localhost ~]# echo "/dev/md0 /RAID_md0 ext4 defaults 0 0" >> /etc/fstab       # 写入挂载信息
        [root@localhost ~]# mount -a        # 重新挂载/etc/fstab文件中记录的所有设备
        [root@localhost ~]# 
        [root@localhost ~]# mdadm  /dev/md0 -f /dev/sdb         # 模拟/dev/sdb设备故障
          mdadm: set /dev/sdb faulty in /dev/md0
        [root@localhost ~]# mdadm -D /dev/md0
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Dec  9 17:10:29 2018
             Raid Level : raid5
             Array Size : 41909248 (39.97 GiB 42.92 GB)
          Used Dev Size : 20954624 (19.98 GiB 21.46 GB)
           Raid Devices : 3
          Total Devices : 4
            Persistence : Superblock is persistent
        
            Update Time : Sun Dec  9 17:17:36 2018
                  State : clean, degraded, recovering 
         Active Devices : 2
        Working Devices : 3
         Failed Devices : 1
          Spare Devices : 1
        
                 Layout : left-symmetric
             Chunk Size : 512K
        
         Rebuild Status : 9% complete
        
                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : b68a693d:68b48e22:eafea3ec:7edb7576
                 Events : 23
        
            Number   Major   Minor   RaidDevice State
               3       8       64        0      spare rebuilding   /dev/sde     # 备份盘已经自动顶替，并开始重建
               1       8       32        1      active sync   /dev/sdc
               4       8       48        2      active sync   /dev/sdd
        
               0       8       16        -      faulty   /dev/sdb           # 故障盘已经被磁盘阵列剔除
        [root@localhost ~]# 	
        	
    逻辑卷管理器(LVM)
    说明：LVM允许用户动态调整硬盘资源；即用户可以在已经创建好的分区上轻松安全地调整分区大小
    建立顺序：物理卷(PV)-->卷组(VG)-->逻辑卷(LV)    
    名称解析：
        物理卷      # 处于LVM的最底层；可以理解为物理硬盘、硬盘分区、磁盘阵列
        卷组       # 由物理卷组成；其中包含多个物理卷；可随时向已存在的卷组中添加删除物理卷
        逻辑卷     # 由卷组中的资源(未使用)建立的；可动态的扩展或缩小空间
    部署命令：
        物理卷
            --> pvscan      # 扫描
            --> pvcreate    # 建立
            --> pvdisplay   # 显示
            --> pvremove    # 删除
        卷组
            --> vgscan      # 扫描
            --> vgcreate    # 建立
            --> vgdisplay   # 显示
            --> vgremove    # 删除
            --> vgextend    # 扩展
            --> vgreduce    # 缩小
        逻辑卷管理
            --> lvscan      # 扫描
            --> lvcreate    # 建立
            --> lvdisplay   # 显示
            --> lvremove    # 删除
            --> lvextend    # 扩展
            --> lvreduce    # 缩小
            # 逻辑卷切割问题
                ---> 以容量为单位：参数为 -L,如 -L 150M  
                ---> 以基本单元个数为单位：参数为 -l，如 -l 37      # 每个基本单元的默认大小为4M，该处为148M



    例子：
        [root@localhost ~]# ls -l /dev/sd*
        brw-rw----. 1 root disk 8,  0 12月 25 2018 /dev/sda
        brw-rw----. 1 root disk 8,  1 12月 25 2018 /dev/sda1
        brw-rw----. 1 root disk 8,  2 12月 25 2018 /dev/sda2
        brw-rw----. 1 root disk 8, 16 12月 24 21:31 /dev/sdb
        brw-rw----. 1 root disk 8, 32 12月 25 2018 /dev/sdc
        [root@localhost ~]# pvcreate /dev/sdb /dev/sdc         # 添加物理卷；让这两块硬盘支持LVM技术
          Physical volume "/dev/sdb" successfully created
          Physical volume "/dev/sdc" successfully created
        [root@localhost ~]# 
        [root@localhost ~]# vgcreate storage /dev/sdb /dev/sdc     # 建立卷组storage，并添加这两个物理卷
          Volume group "storage" successfully created
        [root@localhost ~]# vgdisplay           # 查看卷组
          --- Volume group ---
          VG Name               storage     # 卷组名
          System ID             
          Format                lvm2
          Metadata Areas        2
          Metadata Sequence No  1
          VG Access             read/write
          VG Status             resizable
          MAX LV                0
          Cur LV                0
          Open LV               0
          Max PV                0
          Cur PV                2
          Act PV                2
          VG Size               39.99 GiB
          PE Size               4.00 MiB
          Total PE              10238
          Alloc PE / Size       0 / 0   
          Free  PE / Size       10238 / 39.99 GiB
          VG UUID               hMfCj8-erXL-LmRp-rqsE-cBYT-ZVw6-LQ5f1a
           
          --- Volume group ---
          VG Name               rhel
          System ID             
          Format                lvm2
          Metadata Areas        1
          Metadata Sequence No  3
          VG Access             read/write
          VG Status             resizable
          MAX LV                0
          Cur LV                2
          Open LV               2
          Max PV                0
          Cur PV                1
          Act PV                1
          VG Size               39.51 GiB
          PE Size               4.00 MiB
          Total PE              10114
          Alloc PE / Size       10114 / 39.51 GiB
          Free  PE / Size       0 / 0   
          VG UUID               0fAFJH-GpPN-bq2m-5oyu-Mnf0-8f9C-dImdBw
           
        [root@localhost ~]# 
        [root@localhost ~]# lvcreate -n vo-01 -L 150M storage       # 切割一个150M的逻辑卷设备，名字为vo-01
          Rounding up size to lvreduce -L 120M /dev/storage/vofull physical extent 152.00 MiB
          Logical volume "vo-01" created
        [root@localhost ~]# lvdisplay 
          --- Logical volume ---
          LV Path                /dev/storage/vo-01         
          LV Name                vo-01                  # 这就是新建的逻辑卷设备
          VG Name                storage
          LV UUID                n6cRdm-BmWJ-8Zzc-x7qg-U7Vg-myW9-PDNXO9
          LV Write Access        read/write
          LV Creation host, time localhost.localdomain, 2018-12-25 22:54:04 +0800
          LV Status              available
          # open                 0
          LV Size                152.00 MiB
          Current LE             38
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     8192
          Block device           253:2
           
          --- Logical volume ---
          LV Path                /dev/rhel/swap
          LV Name                swap
          VG Name                rhel
          LV UUID                DQzgjh-MH2C-i0Gs-Gw7r-0G4n-v6gk-qwHNcw
          LV Write Access        read/write
          LV Creation host, time localhost, 2018-10-06 04:24:38 +0800
          LV Status              available
          # open              lvreduce -L 120M /dev/storage/vo   2
          LV Size                1.53 GiB
          Current LE             392
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     256
          Block device           253:1
           
          --- Logical volume ---
          LV Path                /dev/rhel/root
          LV Name                root
          VG Name                rhel
          LV UUID                ki23Sd-0P3i-grNN-yYfG-BAVd-JkbA-LDcFkl
          LV Write Access        read/write
          LV Creation host, time localhost, 2018-10-06 04:24:38 +0800
          LV Status              available
          # open                 1
          LV Size                37.98 GiB
          Current LE             9722
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     256
          Block device           253:0
           
        [root@localhost ~]#
        [root@localhost ~]# mkfs.ext4 /dev/storage/vo-01        # 格式化逻辑卷设备
        mke2fs 1.42.9 (28-Dec-2013)
        文件系统标签=
        OS type: Linux
        块大小=1024 (log=0)
        分块大小=1024 (log=0)
        Stride=0 blocks, Stripe width=0 blocks
        38912 inodes, 155648 blocks
        7782 blocks (5.00%) reserved for the super user
        第一个数据块=1
        Maximum filesystem blocks=33816576
        19 block groups
        8192 blocks per group, 8192 fragments per group
        2048 inodes per group
        Superblock backups stored on blocks: 
        	8193, 24577, 40961, 57345, 73729
        
        Allocating group tables: 完成                            
        正在写入inode表: 完成                            
        Creating journal (4096 blocks): 完成
        Writing superblocks and filesystem accounting information: 完成 
        
        [root@localhost ~]# mkdir /linuxprobe
        [root@localhost ~]# mount /dev/storage/vo-01 /linuxprobe/       #  挂载
        [[root@localhost ~]# df -h
        文件系统                    容量  已用  可用 已用% 挂载点
        /dev/mapper/rhel-root        38G  3.0G   36G    8% /
        devtmpfs                    353M     0  353M    0% /dev
        tmpfs                       362M     0  362M    0% /dev/shm
        tmpfs                       362M  5.4M  357M    2% /run
        tmpfs                       362M     0  362M    0% /sys/fs/cgroup
        /dev/sda1                   497M  119M  379M   24% /boot
        /dev/mapper/storage-vo--01  144M  1.6M  132M    2% /linuxprobe          # 已经可以正常使用了
        [root@localhost ~]# echo "/dev/storage/vo-01  /linuxprobe  ext4  defaults 0 0" >> /etc/fstab    # 写入启动项
        [root@localhost ~]#


    扩容逻辑卷
    # 逻辑卷扩容前需要提前卸载设备与挂载点的关联
    例子：
        [root@localhost ~]# umount /linuxprobe/
        [root@localhost ~]# lvextend -L 290M /dev/storage/vo-01     # 扩展逻辑卷vo-01到290M
        Rounding size to boundary between physical extents: 292.00 MiB
        Extending logical volume vo-01 to 292.00 MiB
        Logical volume vo-01 successfully resized
        [root@localhost ~]# e2fsck -f /dev/storage/vo-01        # 检查硬盘完整性 
        e2fsck 1.42.9 (28-Dec-2013)
        第一步: 检查inode,块,和大小
        第二步: 检查目录结构
        第3步: 检查目录连接性
        Pass 4: Checking reference counts
        第5步: 检查簇概要信息
        /dev/storage/vo-01: 11/30360 files (0.0% non-contiguous), 13580/122880 blocks
        [root@localhost ~]# resize2fs /dev/storage/vo-01        # 重置硬盘容量
        resize2fs 1.42.9 (28-Dec-2013)
        Resizing the filesystem on /dev/storage/vo-01 to 299008 (1k) blocks.
        The filesystem on /dev/storage/vo-01 is now 299008 blocks long.
        
        [root@localhost ~]# mount /dev/storage/vo-01 /linuxprobe/   # 再次挂载磁盘
        [root@localhost ~]# df -h
        文件系统                    容量  已用  可用 已用% 挂载点
        /dev/mapper/rhel-root        38G  3.0G   36G    8% /
        devtmpfs                    353M     0  353M    0% /dev
        tmpfs                 lvreduce -L 120M /dev/storage/vo      362M     0  362M    0% /dev/shm
        tmpfs                       362M  5.4M  357M    2% /run
        tmpfs                       362M     0  362M    0% /sys/fs/cgroup
        /dev/sda1                   497M  119M  379M   24% /boot
        /dev/mapper/storage-vo--01  275M  2.0M  255M    1% /linuxprobe      # 发现磁盘容量已经变化
        [root@localhost ~]#


    缩小逻辑卷
    # Linux系统规定，在对LVM逻辑卷进行缩容操作之前，要先检查文件系统的完整性
    # 在执行缩容操作前需要先把文件系统卸载掉
    例子：
        [root@localhost ~]# umount /linuxprobe/
        [root@localhost ~]# e2fsck -f /dev/storage/vo-01        # 检查文件系统完整性
        e2fsck 1.42.9 (28-Dec-2013)
        第一步: 检查inode,块,和大小
        第二步: 检查目录结构
        第3步: 检查目录连接性
        Pass 4: Checking reference counts
        第5步: 检查簇概要信息
        /dev/storage/vo-01: 11/74888 files (0.0% non-contiguous), 19706/299008 blocks
        [root@localhost ~]# resize2fs /dev/storage/vo-01 120M       # 将逻辑卷vo-1容量重新设定为120M
        resize2fs 1.42.9 (28-Dec-2013)
        Resizing the filesystem on /dev/storage/vo-01 to 122880 (1k) blocks.
        The filesystem on /dev/storage/vo-01 is now 122880 blocks long.
        
        [root@localhost ~]# lvreduce -L 120M /dev/storage/vo-01     # 将逻辑卷vo-1容量缩减到120M  
          WARNING: Reducing active logical volume to 120.00 MiB
          THIS MAY DESTROY YOUR DATA (filesystem etc.)
        Do you really want to reduce vo-01? [y/n]: y
          Reducing logical volume vo-01 to 120.00 MiB
          Logical volume vo-01 successfully resized
        [root@localhost ~]# mount /dev/storage/vo-01 /linuxprobe/
        [root@localhost ~]# df -h
        文件系统                    容量  已用  可用 已用% 挂载点
        /dev/mapper/rhel-root        38G  3.0G   36G    8% /
        devtmpfs                    353M     0  353M    0% /dev
        tmpfs                       362M     0  362M    0% /dev/shm
        tmpfs                       362M  5.4M  357M    2% /run
        tmpfs                       362M     0  362M    0% /sys/fs/cgroup
        /dev/sda1                   497M  119M  379M   24% /boot
        /dev/mapper/storage-vo--01  109M  1.6M   99M    2% /linuxprobe
        [root@localhost ~]# 

    逻辑卷快照
    # LVM具备“快照卷”功能，该功能类似于虚拟机的还原时间点功能
    LVM快照卷功能的特点
        * 快照卷的容量必须等同于逻辑卷的容量
        * 快照卷仅一次有效，一旦执行还原操作则会立即自动删除
    例子：
        [root@localhost ~]# vgdisplay               # 查看卷组信息
          --- Volume group ---
          VG Name               storage
          System ID             
          Format                lvm2
          Metadata Areas        2
          Metadata Sequence No  4
          VG Access             read/write
          VG Status             resizable
          MAX LV                0
          Cur LV                1
          Open LV               1
          Max PV                0
          Cur PV                2
          Act PV                2
          VG Size               39.99 GiB
          PE Size               4.00 MiB
          Total PE              10238
          Alloc PE / Size       30 / 120.00 MiB         # 该卷组中已经使用了120MB
          Free  PE / Size       10208 / 39.88 GiB       # 该卷组空闲容量还有39.88GB
          VG UUID               hMfCj8-erXL-LmRp-rqsE-cBYT-ZVw6-LQ5f1a
           
          --- Volume group ---
          VG Name               rhel
          System ID             
          Format                lvm2
          Metadata Areas        1
          Metadata Sequence No  3
          VG Access             read/write
          VG Status             resizable
          MAX LV                0
          Cur LV                2
          Open LV               2
          Max PV                0
          Cur PV                1
          Act PV                1
          VG Size               39.51 GiB
          PE Size               4.00 MiB
          Total PE              10114
          Alloc PE / Size       10114 / 39.51 GiB
          Free  PE / Size       0 / 0   
          VG UUID               0fAFJH-GpPN-bq2m-5oyu-Mnf0-8f9C-dImdBw
           
        [root@localhost ~]# echo "Welcome to LinuxProbe" > /linuxprobe/readme.txt 
        [root@localhost ~]# ls -l /linuxprobe/
        总用量 14
        drwx------. 2 root root 12288 12月 31 21:05 lost+found
        -rw-r--r--. 1 root root    22 1月   1 07:56 readme.txt       # 这是创建的测试文件
        [root@localhost ~]# lvcreate -L 120M -s -n SNAP /dev/storage/vo-01  # 创建快照卷
          Logical volume "SNAP" created
            # 生成快照卷SNAP；使用 -s 参数生成一个快照卷，使用 -L 参数指定切割的大小；并在命令最后写上是针对哪个逻辑卷执行的快照操作

        [root@localhost ~]# lvdisplay           # 查看逻辑卷信息
          --- Logical volume ---
          LV Path                /dev/storage/vo-01
          LV Name                vo-01
          VG Name                storage
          LV UUID      #           n6cRdm-BmWJ-8Zzc-x7qg-U7Vg-myW9-PDNXO9
          LV Write Access        read/write
          LV Creation host, time localhost.localdomain, 2018-12-25 22:54:04 +0800
          LV snapshot status     source of
                                 SNAP [active]
          LV Status              available
          # open                 1
          LV Size                120.00 MiB
          Current LE             30
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     8192
          Block device           253:2
           
          --- Logical volume ---
          LV Path                /dev/storage/SNAP      # 这就是创建的快照卷
          LV Name      #           SNAP
          VG Name                storage                # 该快照卷所在的卷组
          LV UUID                OZfrG8-eIaV-r9D8-HISf-5iZt-JaCh-m6NqmR
          LV Write Access        read/write
          LV Creation host, time localhost.localdomain, 2019-01-01 07:58:18 +0800
          LV snapshot status     active destination for vo-01       # 该快照卷作用的逻辑卷为vo-01
          LV Status              available
          # open                 0
          LV Size                120.00 MiB
          Current LE             30
          COW-table size         120.00 MiB
          COW-table LE           30
          Allocated to snapshot  0.01%              # 这里显示已经分配的快照比率
          Snapshot chunk size    4.00 KiB
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     8192
          Block device           253:3
           
          --- Logical volume ---
          LV Path                /dev/rhel/swap
          LV Name                swap
          VG Name                rhel
          LV UUID                DQzgjh-MH2C-i0Gs-Gw7r-0G4n-v6gk-qwHNcw
          LV Write Access        read/write
          LV Creation host, time localhost, 2018-10-06 04:24:38 +0800
          LV Status              available
          # open                 2
          LV Size                1.53 GiB
          Current LE             392
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     256
          Block device           253:1
           
          --- Logical volume ---
          LV Path                /dev/rhel/root
          LV Name                root
          VG Name                rhel
          LV UUID                ki23Sd-0P3i-grNN-yYfG-BAVd-JkbA-LDcFkl
          LV Write Access        read/write
          LV Creation host, time localhost, 2018-10-06 04:24:38 +0800
          LV Status              available
          # open                 1
          LV Size                37.98 GiB
          Current LE             9722
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     256
          Block device           253:0
           
        [root@localhost ~]# dd if=/dev/zero of=/linuxprobe/files count=1 bs=100M      # 创建测试文件
        记录了1+0 的读入
        记录了1+0 的写出
        104857600字节(105 MB)已复制，1.0577 秒，99.1 MB/秒
        [root@localhost ~]# lvdisplay                   # 查看逻辑卷
          --- Logical volume ---
          LV Path                /dev/storage/vo-01
          LV Name                vo-01
          VG Name                storage
          LV UUID                n6cRdm-BmWJ-8Zzc-x7qg-U7Vg-myW9-PDNXO9
          LV Write Access        read/write
          LV Creation host, time localhost.localdomain, 2018-12-25 22:54:04 +0800
          LV snapshot status     source of
                                 SNAP [active]
          LV Status              available
          # open                 1
          LV Size                120.00 MiB
          Current LE             30
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     8192
          Block device           253:2
           
          --- Logical volume ---
          LV Path                /dev/storage/SNAP
          LV Name                SNAP
          VG Name                storage
          LV UUID                OZfrG8-eIaV-r9D8-HISf-5iZt-JaCh-m6NqmR
          LV Write Access        read/write
          LV Creation host, time localhost.localdomain, 2019-01-01 07:58:18 +0800
          LV snapshot status     active destination for vo-01
          LV Status              available
          # open                 0
          LV Size                120.00 MiB
          Current LE             30
          COW-table size         120.00 MiB
          COW-table LE           30
          Allocated to snapshot  83.72%         # 这里显示已经分配的快照比率
          Snapshot chunk size    4.00 KiB
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     8192
          Block device           253:3
           
          --- Logical volume ---
          LV Path                /dev/rhel/swap
          LV Name                swap
          VG Name                rhel
          LV UUID                DQzgjh-MH2C-i0Gs-Gw7r-0G4n-v6gk-qwHNcw
          LV Write Access        read/write
          LV Creation host, time localhost, 2018-10-06 04:24:38 +0800
          LV Status              available
          # open                 2
          LV Size                1.53 GiB
          Current LE             392
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     256
          Block device           253:1
           
          --- Logical volume ---
          LV Path                /dev/rhel/root
          LV Name                root
          VG Name                rhel
          LV UUID                ki23Sd-0P3i-grNN-yYfG-BAVd-JkbA-LDcFkl
          LV Write Access        read/write
          LV Creation host, time localhost, 2018-10-06 04:24:38 +0800
          LV Status              available
          # open                 1
          LV Size                37.98 GiB
          Current LE             9722
          Segments               1
          Allocation             inherit
          Read ahead sectors     auto
          - currently set to     256
          Block device           253:0
           
        [root@localhost ~]# umount /linuxprobe/         
        [root@localhost ~]# lvconvert --merge /dev/storage/SNAP     # 对逻辑卷执行快照还原
          Merging of volume SNAP started.
          vo-01: Merged: 24.7%
          vo-01: Merged: 100.0%
          Merge of snapshot into logical volume vo-01 has finished.
          Logical volume "SNAP" successfully removed
        [root@localhost ~]# mount /dev/storage/vo-01 /linuxprobe/
        [root@localhost ~]# ls -l /linuxprobe/      # 再次查看，新建的100M文件已经消失
        总用量 14
        drwx------. 2 root root 12288 12月 31 21:05 lost+found
        -rw-r--r--. 1 root root    22 1月   1 07:56 readme.txt
        [root@localhost ~]#         # 此时再用lvdisplay命令查询，会发现快照卷也被自动删除掉了

    删除逻辑卷
    # 在需要执行LVM的删除操作时，需要自行备份好需要的数据
    # LVM的删除操作顺序是依次删除逻辑卷、卷组、物理卷设备
    例子：
        [root@localhost ~]# umount /linuxprobe/           # 解除挂载信息；以及/etc/fstab中的自动挂载信息
        [root@localhost ~]# lvremove /dev/storage/vo-01     # 删除逻辑卷设备     
        Do you really want to remove active logical volume vo-01? [y/n]: y
          Logical volume "vo-01" successfully removed
        [root@localhost ~]# vgremove storage                # 删除卷组；这里只写卷组名称即可，不需要设备的绝对路径
          Volume group "storage" successfully removed
        [root@localhost ~]# pvremove /dev/sdb /dev/sdc      # 删除物理卷设备
          Labels on physical volume "/dev/sdb" successfully wiped
          Labels on physical volume "/dev/sdc" successfully wiped
        [root@localhost ~]#


0026.iptables与firewalld防火墙
    无论是软件还是硬件防火墙，其主要功能都是依据策略对防火墙自身的流量进行过滤
    防火墙策略可以基于流量的源目的地址、端口号、协议、应用等信息来定制
    防火墙管理工具：            # 防火墙管理工具用于定义防火墙策略
        * iptables工具
            # iptables服务会把配置好的防火墙策略交由内核层的 netfilter 网络过滤器来处理
        * firewalld工具
            # firewalld服务是把配置好的防火墙策略交由内核层的 nftables 包过滤框架来处理

    策略与规则链
        * 防火墙会按从上至下的顺序来读取配置的策略规则
        * 在找到匹配项后就立即结束匹配工作并去执行匹配项中的定义的行为(即放行或阻止)
        * 在读取完所有策略规则后没有匹配项，则会执行默认的策略
        
    防火墙策略规则设置
        * “通”(即放行)
            # 如果防火墙的默认策略为允许时，就要设置拒绝规则，否则谁都能进来
        * “堵”(即阻止)
            # 如果防火墙的默认策略为拒绝时，就要设置允许规则，否则谁都进不来
    
    规则执行
        * Accept        # 允许流量通过(放行)
        * Reject        # 拒绝流量通过(拒绝)，丢弃流量信息，并回复发送者拒绝信息(发送者会看到：目标端口无法访问--Destination Port Unreachable)
        * Drop          # 拒绝流量通过(丢弃)，直接丢弃流量信息，不向发送者回复任何信息(发送者会看到：响应超时的提醒)
        * Log           # 记录日志信息
           
            
    iptables工具
    iptables 命令可以根据流量的源地址、目的地址、传输协议、服务类型等信息进行匹配,一旦匹配成功,iptables 就会根据策略规则所预设的动作来处理这些流量。另外,再次提醒一下,防火墙策略规则的匹配顺序是从上至下的,因此要把较为严格、优先级较高的策略规则放到前面,以免发生错误。
    
    规则链(chains)
    说明：iptables服务把用于处理或过滤流量的策略条目称之为规则，多条规则可以组成一个规则链
    规则链根据数据包处理位置的不同，分类如下：
        * Prerouting        # 对数据包作路由选择前应用此链中的规则(注意：所有的数据包进来的时侯都先由这个链处理)
        * Input             # 进来的数据包应用此规则链中的策略
        * Output            # 外出的数据包应用此规则链中的策略
        * Forward           # 转发数据包时应用此规则链中的策略
        * Postrouting       # 对数据包作路由选择后应用此链中的规则(注意：所有的数据包出来的时侯都先由这个链处理)
     
    规则链之间的优先顺序情况
        * 第一种情况:入站数据流向
          从外界到达防火墙的数据包，先被Prerouting规则链处理(是否修改数据包地址等)，之后会进行路由选择(判断该数据包应该发往何处),如果数据包的目标主机是防火墙本机(比如:这是Internet用户访问防火墙主机中的web服务器的数据包),那么内核将其传给Input链进行处理(决定是否允许通过等),通过以后再交给系统上层的应用程序(如Apache服务器)进行响应
        * 第二种情况:转发数据流向
          来自外界的数据包到达防火墙后,首先被Prerouting规则链处理,之后会进行路由选择,如果数据包的目标地址是其它外部地址(比如局域网用户通过网关访问QQ站点的数据包).则内核将其传递给Forward链进行处理(是否转发或拦截),然后再交给Postrouting规则链(是否修改数据包的地址等)进行处理
        * 第三种情况:出站数据流向
          防火墙本机向外部地址发送的数据包(比如在防火墙主机中测试公网DNS服务器时),首先被Output规则链处理,之后进行路由选择,然后传递给Postrouting规则链(是否修改数据包的地址等)进行处理

    iptables规则表(tables)   # 表用于提供特定的功能
    (iptables规则表之间的优先顺序：Raw--->Mangle--->Nat--->filter)
        * filter表          
        # 实现数据包过滤;使用的内核模块为:iptables_filter
            --Input
            --Forward
            --Output
        * nat表             
        # 实现网络地址转换(IP,端口);使用的内核模块为:iptable_nat
            --Prerouting
            --Postrouting
            --Output
        * mangle表          
        # 实现数据包重构(修改)--修改数据包的服务类型、TTL、并且可以配置路由实现QOS；使用的内核模块:iptable_mangle
            --Prerouting
            --Postrouting
            --Input
            --Output
            --Forward
        * raw表             
        # 决定数据包是否被状态跟踪机制处理；使用的内核模块：iptable_raw
            --Output
            --Prerouting
    
    iptables基本语法格式
        iptables  [-t  表名]  命令选项  [链名]  [条件匹配]  [-j  目标动作或跳转]
        说明:
            * 表名,链名用于指定iptables命令所操作的表和链
            * 命令选项用于指定管理iptables规则的方式(如:插入,增加,删除,查看等)
            * 条件匹配用于指定对符合什么样条件的数据包进行处理
            * 目标动作或跳转用于指定数据包的处理方式(如:允许通过,拒绝,丢弃,跳转--Jump给其它链处理)
                           
    iptables常用参数
        * -P        # 设置默认策略
        * -F        # 清空规则链
        * -L        # 查看规则链
        * -I num    # 在规则链头部加入新规则
        * -D num    # 删除某一条规则
        * -s        # 匹配来源地址 IP/MASK ，加叹号"!"表示除这个IP外
        * -d        # 匹配目标地址
        * -i 网卡名称       # 匹配从这块网卡流入的数据
        * -o 网卡名称       # 匹配从这块网卡流出的数据
        * -p        # 匹配协议，如TCP、UDP、ICMP
        * --dport num      # 匹配目标端口号
        * --sport num      # 匹配来源端口号
    
    例如：
        [root@localhost ~]# iptables -L     # 查看规则链
            Chain INPUT (policy ACCEPT)         # 默认策略：允许流量通过
            target(目标) prot(端口) opt(选择) source(来源) destination(目标)
            ACCEPT all -- anywhere anywhere ctstate RELATED,ESTABLISHED
            ACCEPT all -- anywhere anywhere
            INPUT_direct all -- anywhere anywhere
            INPUT_ZONES_SOURCE all -- anywhere anywhere
            INPUT_ZONES all -- anywhere anywhere
            ACCEPT icmp -- anywhere anywhere
            REJECT all -- anywhere anywhere reject-with icmp-host-prohibited
            ......省略很多输出......
        [root@localhost ~]# iptables -F     # 清空已有的防火墙规则链
        [root@localhost ~]# iptables -P INPUT DROP          # 将INPUT规则链的默认策略设置为拒绝
            # 注意：如果此时是远程连接的话，连接会立刻断开
        [root@localhost ~]# 

































































