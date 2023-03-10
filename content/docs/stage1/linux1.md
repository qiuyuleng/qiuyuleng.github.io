---
title: "Linux1"
typora-root-url: ../
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

# 第零章 计算机基础
操作系统是连接硬件和用户的，让用户不用对着机器文档编程，更方便
操作系统用来有效率地控制计算机的硬件资源，并且提供方便程序员编程的接口

# 第一章 Linux是什么
网路基础：
http://www.study-area.org/network/network.htm

在Linux系统中， 每个设备都被当成一个文件来对待



# 第二章 主机规划与磁盘分区
# 第三章 安装一个Linux系统s
简单翻过去了

# 第四章 首次登陆与线上求助
man指令学到了很多小技巧，比如/search
一些数字的含义
如果忘记指令了怎么办？ man后面的一些后缀
nano
vimtutor

# 第五章 Linux文件权限与目录配置
各种权限查看和更改

- ls -l

- chgrp, chown, chmod
  各种权限是什么含义，尤其是目录文件的
  注意目录的x权限
  绝对目录、相对目录

# 第六章 Linux文件与目录管理

- .和..和~

- PATH
- rmdir 删除空目录 rm-r 删除不空的目录
- mv除了移动文件，也可以用作文件的更名
- 管线|：前一个指令输出的讯息，通过管线交给后续的指令继续使用
  - eg：cat -n /etc/man_db.conf | head -n 20 | tail -n 10
- 分号；：在一行中输入多个指令
- touch 创建新的空文件，修改 /atime（access读取时间）和mtime（modify修改时间），不能修改ctime（status time）
- 查阅文件内容 cat, less, more, tail, head
- 默认权限 umask
  - 文件 666 默认没有x（不可执行）
  - 目录 777 默认有x（可以进入目录）
- 特殊权限 SUID、SGID、SBIT
  - 4 SUID：使用者执行这一程序时，执行过程中会暂时拥有程序拥有者的权限，例如：passwd修改自己的密码
  - 2 SGID：使用者在这个目录下新建的文件的群组都会和该目录的群组名称相同
    - 小组作业，小组内部成员可以同一个目录底下工作，但是小组成员还是拥有自己的家目录和私有群组，那么可以新建小组群组并将小组成员添加到这个群组，新建小组任务文件夹，然后将该文件夹权限设置为2770（13章任务二例题）
  - 1 SBIT：该目录下使用者创建的文件只有自己与root能够删除
- chattr和lsattr设置观察隐藏属性
- which查指令
- whereis查文件很快，后面要接文件的完整名字
- locate查文件也很快，并且后面不需要接完整的名字，但是因为查的是数据库里的数据，所以有时候需要updatedb来更新数据库
  - updatedb执行比较慢
- find 很万能
  - find [PATH] [option]
  - e.g., find / -name '*passwd*'

# 第七章 Linux磁盘和文件系统

- ext文件系统: superblock, inode, block
  - journal: 多一个记录区记录系统主要活动，可以加快系统复原时间
- xfs文件系统: 格式化快，能处理大文件
- hard link和symbolic link
  - hard link 实体链接多了一个文件名对该inode号码的链接
  - symbolic link 符号链接，类似win的快捷方式
- 分区gdisk，格式化mkfs，挂载mount
- 内存置换空间swap

# 第八章 压缩打包与备份

- 压缩: gzip, bzip2, xz (压缩比越来越高，压缩速度越来越慢)
- 打包: tar
  - 压缩+打包的文件叫做tarball
  - ![image-20230221104200221](/../img/image-20230221104200221.png)
- 备份：xfsdump, xfsrestore

# 第九章 Vim

- :sp 可以分窗口，ctrl+w+上/下 可以控制不同的窗口
- 其他的可以现查
- 暂存档 .swp

# 第十章 BASH

- 之前的指令在.bash_history中

- ![image-20230221105647179](/../img/image-20230221105647179.png)

- `echo $variable`

- 定义变量 `myname=VBird`

- 环境变量env, export, 所有变量set

  - export variable, 将自定义变量变成环境变量，分享自己的变量设定给后来的其他程序

- 取消变量 unset

- read -pt variable 读取键盘输入作为变量

- declear 声明变量的类型

- shell里面的很多设置和配置文件介绍

- source 配置文件名，不需要注销再登录，直接将修改后的设定写入配置文件

- stty [-a] seeting tty的各个按键参数（例如 ctrl c等）

- bash中的特殊符号与通配符

  - $?表示前一个指令执行完毕后的回传值，为0表示执行成功 
  - ![image-20230221110555075](/../img/image-20230221110555075.png)
  - ![image-20230221110635789](/../img/image-20230221110635789.png)
  - 双星号
    - **/*.war: 所有文件夹下的 .war 文件
    - *.war: 当前文件夹下的 .war 文件

- 数据流重导向：指令执行后应该出现在屏幕上的数据给传到其他地方去

  - \>, >>, 2>, 2>>
    - example 1: command > doc1 2> doc2
      - stdout存到doc1
      - stderr存到doc2
    - example 2: command 2> /dev/null
      - 黑洞装置/dev/null，将错误信息吃掉，既不显示在屏幕上又不储存
    - example 3: command > doc 2>&1
      - 将stdout和stderr写入同一个文件
        同理也有 1>&2
  - <, <<
    - < 用文件内容取代键盘输入
    - << 后接结束的输入字符
  - tee：同时输出给文件和屏幕
    - e.g.: python run.py 2>&1 | tee train.log 

- 多条命令一次输入来执行

  - cmd1; cmd2
    - 指令之间没有相关性。
  - cmd1 && 或 || cmd2
    - 前一个指令是否成功执行与后一个指令是否要执行相关
    - 假设判断式：cmd1 && cmd2 || cmd3

- 管线pipe

  - 与连续下达命令的区别？

  - | ：只能处理前一个指令传来的正确信息, i.e., stdout, 不能处理错误信息

    - ![image-20230221115142361](/../img/image-20230221115142361.png)

    - 能够接受std input的命令才是管线命令
      - less, more, cat等可以
      - ls, cp, mv 等这些不行
    - 硬要处理错误信息的话，就用数据流重导向2>&1

  - 撷(xie 2)取命令

    - 行 为单位
    - cut
      - 处理特定分割的数据，例如echo ${PATH}中有"："
      - 处理排列整齐的数据，例如export
      - 处理多空格相连的数据比较费劲
    - grep
      - 取出有特定信息‘text’的那一行

  - 排序命令 sort

  - 重复的信息只显示一个 uniq，配合排序过的文件来处理，先sort再uniq

  - 输出数据各种计数 wc：行、字数、字符数

  - 同时将数据流输出给文件和屏幕：tee

  - tr 删除或者替换文字

  - col 将tab键转换成对等的空格键

    - 和expand什么区别？

  - join 将两个文件中有 相同数据 的那一行加在一起

    - join之前，先排序

  - paste 将文件两行贴在一起，用tab来分隔

    - file如果写成-，代表资料来自standard input

  - expand将tab转成空格键

    - 和col什么区别？

  - 分区命令 split 将大文件分成小文件

    - 分区之后合并，用数据流重导向>>就可以了

  - 参数代换 xargs：产生某个指令的参数

    - xargs可以读入stdin的数据，并且以空白或者断行字符作为分辨，将stdin的数据分隔成arguments
    - xargs [-0epn] command
      - -n 后面接次数，每次command执行需要几个参数的意思
    - 主要用法：很多指令不是管线命令，用xargs来引导这些指令使用std input
      - id command的例子
    - 另一个用法：数据量很大，使用xargs每次丢几个给command处理（11章第一个例题）

  - “-” 前一个指令的stdout作为这次的stdin，那么stdout和stdin可以用“-”来替代

- 一个区分：type 指令是何种类型，file 是文件是什么类型

# 第十一章 正规表示法与文件格式化处理

- 正规表示法，regular expression, 正则表达式

- 搜索、删除、取代特定的字符串，以行为单位

- 有些工具支持正规表示法，比如grep，有些不支持，比如cp, ls，它们只能使用bash自己本身的通配符

- 与通配符有什么区别？

  - 通配符(wildcard)是bash操作环境里的东西，是bash的一个功能
  - 正则表示法是字符串处理的一种表示方式

- 基础正规表示法

  - 搜索特定字符串 grep 'text' file
  - 利用中括号[]搜索集合字符
    - e.g., 't[ae]st', []里面代表一个字符，这个字符是a或者e
    - e.g., '[^a-z]oo', oo前面不能是小写字母
    - e.g., '[^[:lower:]]oo', oo前面不能是小写字母
  - 行首^，行尾\$
    - e.g., '^the', 以the开头
    - e.g., '^[a-z]' 或者 '^[[:lower:]]', 以小写字母开头
    - "^"符号在字符集合符号[]内外的含义不同，在[]内表示反向选择，在[]外表示行首
    - e.g., `\.$`结尾的行，注意使用跳脱字符\
    - 空白行`^$`
  - "."一定有一个任意字符，"\*"重复前一个字符0到无数次
    - 'o\*'指的是空字符或者一个及以上o, 'oo\*' 指的是有一个以上o
    - '.*' 指零个或多个任意字符
  - {} 限定字符范围
    - e.g., 
    - e.g., 'go\\{2,5\\}g', g后面接2到5个o，再接一个g
      - 注意使用跳脱字符\
    - e.g., 'go\\{2,\\}g', 接2个以上o
  - sed工具
    - 是个管线命令，用来分析standard input
    - 后面接的动作，要用单引号' '括住
      - 如果接超过两个以上的动作，那么每个动作前要加上 -e
    - 功能
      - 新增、插入、删除
      - （整行）取代、显示
      - 部分数据的搜寻取代 sed 's/要被取代的字符串/新的字符串/g'
      - -i 直接修改文件内容，要小心

- 延伸正规表示法

  - egrep
  - "+" 一个或一个以上前一个字符：o+表示一个以上的o
  - "?"零个或一个前一个字符：o? 表示空或者一个o
  - "|" 或，'gd|good', gd或good
  - "()" 找出群组字符串
    - 搜寻glad或good：g(la|oo)d
  - "()+" 重复群组

- 格式化打印 printf

  - printf '打印格式' 实际内容
  - 不是管线命令 `printf '%s\t %s\t %s\t \n' $(cat printf.txt)`
  - %8.2f, 五位整数，两位小数，一位小数点，总共八位

- 数据处理工具awk

  - 是个管线命令，可以处理std input或者文件
  - `$1$`,` $2$` ... 代表第x个字段
    - 字段间以空格或者tab键隔开 
    - `$0$` 代表一整行
  - 每次处理一行，字段是最小处理单位
  - BEGIN 关键词 预先设定awk的变量
  - awk里面的''变成“”

- 文件对比

  - diff
    - 是管线命令
    - 以行为单位对比
    - 除了对比文件，也可以对比目录
  - cmp
    - 也是管线命令
    - 只能对比文件
    - 以字节为单位对比
  - patch
    - 补丁档，将旧的文档升级到新的文档
    - 比较新旧版本的差异，将差异档制作成为补丁档，用补丁档更新旧文件

- 文件打印 pr

- 文件时间，文件档名，页码
  # 第十二章 Shell Scripts

- 如何执行一个shell scripts文件（首先要有rx权限）

  - /绝对路径/shell.sh
  - 相对路径，./shell.sh
  - 将shell.sh放在PATH目录里，然后可以直接 shell.sh
  - bash shell.sh （只要有r权限就可以）
  - source shell.sh 【在父程序bash执行】

- 写一个shell script程序

  - 第一行 #!/bin/bash

  - 注释说明
    - script的功能，版本信息，较特殊的需要使用绝对路径的方式来下达的指令，需要的环境变量宣告与设定
  - 宣告环境变量
  - 主要程序
  - 执行结果告知（回传值）

- 数值计算 `var=\$((运算内容))`

- 判断式

  - test指令
    - 判断文件是否存在，什么类型权限，文件比较，字符串数据等
    - 反状态，test ! -x file
  - 判断符号 [ ]
    - 每个组件之间都需要空格来分隔
    - 变量需要用双引号括号起来
    - 常量需要用单或双引号括号起来
    - e.g., `[ [] "$HOME"[]==[]"$MAIL"[] ]`
    - 经常用在条件判断式中 if...then...fi
  - 默认变量
    - 可以通过 script.sh variable 来直接给变量，也可以通过read交互式 
    - \$0: 文件名
    - `$1, $2, $3 ...` 第一、二、三...个参数
    - `$#`: 参数数目
      `$@`: 全部的参数内容，i.e.,`"$1" "$2" "$3" "$4"......`
    - 变量偏移shift number，拿掉最前面的几个参数

- 条件判断式

  - 单层、简单的

    ```
    if [条件判断式]; then
        ...
    fi
    ```

    - [] || [] 或者 &&

  - 多层的

    - if [条件判断式]; then ... else ... fi
    - if [条件判断式一]; then ... elif [条件判断式二]; then ... else ... fi

  - case

    - ![image-20230221203003800](/../img/image-20230221203003800.png)

- function功能

  - ![image-20230221203051087](/../img/image-20230221203051087.png)
  - 要放在程序最前面
  - 也有默认变量 `$0, $1, $2.....`
    - `$0`指的是function函数名称
    - `$1, $2 ...`都和script的不一样，是函数的

- 循环 loop

  - 不定循环
    - ![image-20230221203645822](/../img/image-20230221203645822.png)
    - ![image-20230221203715297](/../img/image-20230221203715297.png)

  - 固定循环
    - ![image-20230221203845941](/../img/image-20230221203845941.png)
      - var的值分别是con1，con2，con3
    - `$(seq ...)`
      - e.g.: $(seq 1 100)
      - 也可以用 {1..100}
    - ![image-20230221204018580](/../img/image-20230221204018580.png)
      - 可以用 i++

- shell script 追踪与debug

  - bash [-nvx] scripts.sh
  - 书上的command用的是sh，但是其实是bash，我没有设置sh是bash的alias，如果用sh的话，for数值循环会报错

# 第十三章 账号管理与ACL权限设定

- 账号与群组
  - /etc/passwd 文件
  - /etc/shadow 文件
  - /etc/group 文件
    - 群组、有效群组（command groups 出来的第一个群组，作用在新建文件）、初始群组（passwd中的第四栏）
    - newgrp来切换有效群组，是以另一个shell来提供这个功能的，所以结束后要exit
      - ![image-20230221204357899](/../img/image-20230221204357899.png)
  - /etc/gshadow 文件，用来设置群组管理员
- 账号管理
  - useradd建立账号
    - -s /sbin/nologin 使得用户无法登入系统取得shell
  - /etc/skel 用户家目录的参考基准目录，家目录中的默认数据是由这个目录复制过去的
  - /etc/login.defs  UID/GID/密码参数等
  - passwd 修改（设置）密码
  - chage 详细的密码参数
  - usermod 微调useradd的参数 
    - root通过usermod -a user1 -G group1 将user1新加到group1
  - userdel 删除用户数据
  - finger, chfn, chsh
  - groupadd, groupmod, groupdel
    - 要删除群组，要确认没有账号使用该群组作为初始群组，否则无法删除
  - 群组管理员 gpasswd
  - 小组作业，小组内部成员可以同一个目录底下工作，但是小组成员还是拥有自己的家目录和私有群组，那么可以新建小组群组并将小组成员添加到这个群组，新建小组任务文件夹，然后将该文件夹权限设置为2770（13章任务二例题）
  - 外部身份验证系统 authconfig-tui: CentOS的，Ubuntu没有这个指令
- 主机的细部权限规划: ACL的使用
  - Access Control List，为单一的用户或群组设定权限
  - 设定ACL权限 setfacl，查找ACL权限 getfacl
    - setfacl -R 与  setfacl -m d....的区别：前者是递归设定之前已经存在的文件的ACL权限，后者是未来文件的ACL权限
    - 要设定一个用户/群组没有任何ACL权限，权限的地方不能直接留白，要加上一个“-”减号
- 使用者身份切换
  - su
    - "-":  使用login shell的方式切换身份，完整切换
    - " "：使用non-login shell的方式
  - sudo
    - 能否执行sudo要看/etc/sudoers
      - 使用visudo来修改/etc/sudoers，不能直接vi /etc/sudoers
    - 后接用户自己的密码来确认执行后续的指令
    - 可以设置 sudo su -
- 用户的特殊shell与PAM模块
  - PAM这块书上是以CentOS为例的，我在ubuntu上没有找到文件在哪里啊
    - 找到了，/usr/lib/x86_64-linux-gnu/security/
- 用户信息传递
  - 查询使用者：w, who, lastlog
  - 使用者对谈：write
    - 拒绝接收信息 mesg n，但无法拒绝来自root的信息
  - 对所有系统上在线的用户广播：wall
  - 邮件：mail
- 账号检查工具
  - Ubuntu 的passwd指令没有–stdin
    - 可以用chpasswd
  - 一个大量新建设置账号的脚本模板

# 第十四章 磁盘配额(Quota)与进阶文件系统管理

- 什么是Quota? 让磁盘容量公平的分配

  - 我的文件系统是ext4，并且/home不是独立的文件系统（mounted on 根目录），所以先跳过了14.1这一节

- 磁盘阵列RAID: 将多个较小的磁盘整合成为一个较大的磁盘装置

  - RAID选择的不同level

    - RAID-0：等量模式 stripe。只要有任何一颗磁盘损毁，在RAID上面的所有数据都会遗失无法读取
    - RAID-1：映像模式 mirror。同一份数据完整保存在两颗磁盘上。
    - RAID 1+0：最推荐
      - ![image-20230221205449948](/../img/image-20230221205449948.png)

    - RAID-5：有同位检查码，可以支持一颗磁盘损毁的情况
      - ![image-20230221205531404](/../img/image-20230221205531404.png)
      - 同为检查数据（奇偶校验位）是什么？Parity占的存储空间不会是A0+B0的总和吗？
        - 我理解的奇偶校验只能知道传输有没有错误，不能用来恢复啊。好像也可以？
      - 为什么是round robin写入的？
      - 另有RAID-6，允许同时两颗磁盘损毁的情况

  - 预备磁盘 spare disk

    - 不支持热插拔的话关机才能动手插拔硬盘

  - 一些优点

    - 数据安全和可靠性：硬件损毁的话能不能安全恢复
    - 读写：RAID 0可以改善系统I/O
    - 扩大容量

  - 因为硬件磁盘阵列很贵，所有利用软件来仿真磁盘阵列的功能 → 软件磁盘阵列

    - 第七章的练习没怎么做
      - 我遇到的问题 gdisk /dev/sda

- 逻辑滚动条管理员
  - 实现一个可以弹性调整容量的文件系统

第十五章 例行性工作排程（crontab）

- 什么是？ 将Linux的例行性工作安排执行的流程
  - 分类
    - at：仅执行一次，突发性的
    - crontab：例行性的，每隔一定周期就要办的事情
  - 一些例行性工作
- 仅执行一次的工作排程
  - 确保atd启动
  - /etc/at.deny
  - at指令
    - 背景执行功能
  - atq查询，atrm删除指令
  - batch指令
    - 在CPU的工作负载小于0.8时，再进行工作任务
- 循环执行的例行性工作排程
  - 默认启动
  - crontab指令
  - 一些注意事项
    - 资源分配不均
    - 周和日月不可同时共存
  - 可唤醒停机期间的工作任务
    - anacron 主动帮忙执行时间到了但是却没有执行的排程
    - 时间戳

# 第十六章 进程管理与SELinux初探

- 什么是进程？
  - 进程是一个正在运作中的程序
  - 进程呼叫 fork and exec
  - 服务deamon：常驻在内存中的进程，文件名以d结尾
- 工作管理 job control
  - 工作管理是当我们登入系统取得bash shell之后，在单一终端机接口下同时进行多个工作的行为管理
  - 管理的工作都是当前bash的子进程
  - 前景foreground，背景background
    - vim不可能在背景中running
  - job control的管理
    - 直接将指令丢到背景中执行：command+ &，最好使用数据流重导向
    - 将目前的工作丢到背景中，并暂停：ctrl + z
    - 观察目前的背景工作状态：jobs
    - 将背景工作拿到前景来处理：fg
      - %+数字
    - 让工作在背景下的状态从暂停变成运行中：bg
    - 移除工作：kill
  - 脱机管理
    - 要注意工作管理的背景仍然和终端机有关，脱机注销之后工作不会继续进行，会中断
    - 工作需要一大段时间，要如何处理
      - 使用at
      - tmux
      - nohup
- 进程管理
  - 进程的观察
    - ps：某个时间点的进程运作情况
      - 僵尸进程 defunct
      - ps aux 所有进程
      - ps -l 自己的bash相关进程
    - top：动态观察进程的变化
    - pstree：更直观地看进程相关性
  - 进程的管理
    - 信息传递 signal
      - 常用编号
        - 1：重新启动
        - 9：强制中断
        - 15：正常中止
      - kill -signal PID/%jobnumber
      - killall -signal 指令名称
        - 不需要配合ps来找到进程的ID，而是直接用名称
  - 进程的执行顺序 (priority)
    - CPU排程：每支进程被CPU运行的演算规则
    - 优先执行序 priority，PRI：越低越优先
      - 核心动态调整的，用户无法直接调整PRI值
      - 优先级高的程序每秒被运作的次数更多
    - 用户可以条件的是NI值，i.e., nice值
      - 条件范围-20~19，一般使用者无法调到负值
      - 指令：nice，renice
      - 修改nice值可以在父进程子进程之间传递
    - 系统资源的观察
      - free 内存使用情况
      - uname 查询系统与核心相关信息
      - uptime 系统启动时间与工作负载
      - netstat 追踪网络或插槽文件
      - dmesg 分析核心产生的讯息
      - vmstat 侦测系统资源变化，看看哪个部分的资源被使用的最频繁
- 特殊文件与进程
  - 有SUID和SGID权限的指令执行状态
    - 程序与进程的区别：SUID程序在运行过程中产生的进程
  - /proc 里面的文件
    - 查询已开启文件或者已执行进程开启的文件
      - fuser：某个文件（或文件系统）目前正被哪些进程所利用
      - lsof：某个进程开启或者使用的文件与装置
      - pidof：某支正在被执行的程序的PID
        - 另外也可以用 ps aux 配合 grep + 正则表达式
- SELinux
  - Security Enhanced Linux，安全强化的linux
  - 传统的文件权限与账号关系，i.e.，自主式访问控制DAC比较危险，所有使用依据政策规则制定特定进程读取特定文件的委托式访问控制MAC
  - 运作模式
    - 主体：进程；目标：文件资源
    - ![image-20230221210528803](/../img/image-20230221210528803.png)
    - 安全性本文可以想成SELinux内必备的rwx
    - SELinux的重要属性：Type（文件资源）/domain（进程)
  - 三种模式：
    - 强制、宽容、关闭
    - 要切换模式的话得重新启动

# 第十七章 系统服务daemons

- deamons和服务是什么？
  - 达成某个服务的程序叫做daemon
  - systemd启动服务机制
  - 一些概念：
    - 服务单位unit，服务类型type：每种服务单位按照功能来划分，可以分成不同的服务类型
    - 操作环境target是服务类型的一种，是一群unit的集合
  - 主要服务类型
  - systemd启动过程中，无法与管理员透过standard input传入信息
- systemctl管理服务
  - 单一服务的 systemctl [command] [unit]
  - 如果用kill来关闭一个正常的服务，那么systemctl就无法继续监控这个服务了
  - deamon的各种状态：active，enabled等
  - 注意service部分用start/stop/restart，target项目用isolate
  - systemctl list-dependencies
  - 网络服务与对应端口
- systemctl针对service类型的配置文件
  - 配置文件相关目录
  - 配置文件的设定项目
    - 配置文件整体分为三个部分：Unit, Service, Install
    - 每个部分下面有很多参数
  - 简化多个执行的启动设定
    - ![image-20230221210832375](/../img/image-20230221210832375.png)
    - 将执行文件中的@范例名称带入到源文件中的%I
- systemctl针对timer的配置文件
  - 和crond类似，定期执行任务
  - 在ubuntu上叫timers.target

# 第十八章 认识与分析登录档

- 登录档是什么：记录系统在什么时候由哪个程序做了什么样的行为，发生了何种的事件

  - 一般只有root有权限读取
  - 常见的登录档
    - /var/log/syslog

- rsyslog.service

  - stand alone deamon 

  - 配置文件 /etc/rsyslog.conf

    - 语法在这里 /etc/rsyslog.d/50-default.conf
    - 服务名称、讯息等级、被记录在哪里
      - 服务名称与讯息等级之间的连接符号【.】表示>=后面严重程度的都记录下来
      - 不需要登录等级 .none

    - 文件名前的“-”：先将讯息存储在速度较快的内存中，等数据量够大了再一次性填入磁盘内
      - 编辑登录档后restart rsyslog.service
    - 可以添加chattr属性来保护登录档，防止手误更改了文件

  - 一些区分

    - ![image-20230221211443403](/../img/image-20230221211443403.png)
    - 登录档服务器

- 登录档的轮替（logrotate）

  - logrotate是挂在cron底下进行的
  - 主要功能
    - 将旧的登录档改名字移动成旧档，然后新建新的登录档，旧的登录档一段时间后删除

  - 配置文件
    - /etc/logrotate.conf
    - /etc/logrotate.d
    - 基本语法
      - 档名，得是绝对路径
      - 参数，monthly等等
      - 执行脚本，需要sharedscripts, prerotate, postrotate, endscript
    - 加了chattr的a属性后不能，文件不能直接更名成功，需要在配置文件里先把a参数去掉，然后再更名，然后再加上这个属性

- systemd-journald.service

  - systemd-journald.service用来管理与查询这次开机后的登录信息，而rsyslogd用来记录以前及现在的数据到磁盘中，方便未来查询
  - journalctl指令：查询systemd-journald.service的数据
  - logger指令：将我的数据存储到登录文件中

- 分析登录档

  - logwatch

# 第十九章 开机流程、模块管理与Loader

- Linux开机流程分析

  - ![image-20230221211734875](/../img/image-20230221211734875.png)

  - 第一支程序systemd

    - PID号码是1号

    - 准备软件执行的环境，包括主机名、网络设定等服务的启动

    - 操作环境target

    - 通过systemctl list-dependencies graphical.target 来观察systemd的大致开机流程

      - ![image-20230221211835667](/../img/image-20230221211835667.png)

      - 脚本放在/etc/rc.d/rc.local文件里，可以开机执行

    - 用到的主要配置文件

      - 加载额外的核心参数设定/etc/modules-load.d/*.conf、/etc/modprobe.d/*.conf
      - 常见的环境配置文件 /etc/sysconfig/*

- 核心与核心模块

  - 核心（kernel），目前都具有可读取模块化驱动程序的功能，也就是modules模块化，可以把它想象成插件
  - 核心相依性 modules.dep
  - 显示所有模块 lsmod
  - 模块信息 modinfo
  - 加载模块 modprobe，会自动检查相依性

- Boot Loader：Grub

  - 略过这一节了

- 开机过程的问题解决

  - 忘记root密码
  - 开机以root执行bash
  - 文件系统错误

# 第二十章 基础系统设定与备份策略

- 系统基本设定
  - 网络设定
    - nmcli指令来手动设定
    - DHCP自动分配
    - hostnamectl 主机名
  - 日期和时间设定
    - timedatectl 显示时区时间
  - 语系设定
  - 防火墙简易设定
- 服务器硬件数据收集
  - dmidecode解析硬件配备
  - 观察硬件状态信息的指令：gdisk, dmesg, vmstat, lspci, lsusb, iostat
  - 了解磁盘的健康状态 smartd
    - 虚拟机不支持
- 备份
  - 要备份哪些数据
    - /etc
    - /home
    - /root
    - /var/spool/mail/, /var/spool/cron/, /var/spool/at/
    - /var/lib/
  - 备份的种类
    - 完整数据备份
      - 累积备份
      - 差异备份
    - 关键数据备份
  - 备份策略
    - 每周备份的script
      - cp、tar、crontab
    - 每日备份的script
      - cp、tar、crontab
    - 远程备援的script
      - rsync、ssh

# 第二十一章 软件安装：原始码与Tarball

- 开放源码的软件安装

  - 开放源码、编译程序、可执行文件 
    - 什么是可执行文件：二进制文件
    - 什么是开放源码：程序代码，纯文档
    - 什么是编译程序：将程序代码转译成机器看得懂的语言
    - 程序代码（纯文档）通过编译程序，利用已存在的函数库，编译成可执行文件
    - ![image-20230221212320963](/../img/image-20230221212320963.png)

  - 函式库
    - 什么是函式库：被呼叫来执行的一段功能函数
  - make和configure
    - make，makefile
    - 通过侦测程序主动建立makefile，侦测程序的文件名一般为configure或config
    - ![image-20230221212418704](/../img/image-20230221212418704.png)

  - Tarball软件
    - 将源代码打包压缩，解压后里面一般有
      - 源代码文件
      - 侦测程序文件（configure或者config）
      - 简易说明README、或者INSTALL

- 使用C语言进行编译的简单范例

  - gcc hello.c 直接编译原始码
  - gcc -c 仅编译成目标文件object file，并不制作链接等功能
    - 产生hello.o，不产生二进制执行档
    - 原始码文件并非只有一个文件，无法直接进行编译

- 用make进行宏编译

  - 简化编译时需要下达的指令

  - 仅针对修改了的文件编译，其他的object file不会被更动

  - 基本语法

    - ![image-20230221212555607](/../img/image-20230221212555607.png)

    - #批注

  - 变量

    - ![image-20230221212636401](/../img/image-20230221212636401.png)
    - ![image-20230221212950753](/../img/image-20230221212950753.png)

    - $@ 代表当前的目标 target
    - 环境变量取用规则
      - make指令列后面加上的环境变量优先
      - makefile里面的第二
      - shell原本的环境变量第三

- Tarball的管理与建议

  - ![image-20230221212739959](/../img/image-20230221212739959.png)

  - 大部分安装流程需要的指令
    - .configure
    - make clean
    - make
    - make install
  - 建议软件安装在/usr/local下，原始码tarball放在/usr/local/src下
  - 利用diff，patch更新软件
    - patch的功能是更新原始码文件，还是要将软件进行编译后，才能最终得到正确的软件
    - 如果版本跨度较大没有对应的patch文件，需要依序更新

- 函式库管理

  - 动态与静态函式库
    - 静态函式库 .a：编译的时候直接整合到执行程序当中，编译后的可执行文件可以独立执行
      - 函式库升级后，整个执行档必须重新编译
    - 动态函数库 .so，没有整合到执行文件当中，程序里面只有一个指针pointer的位置，编译出来的程序不能独立执行
      - 函式库文件必须要存在，所在的目录也不能改变
      - 函式库更新后，无需重新编译，函式库的升级方便
  - 将常用的动态函式库加载到内存中，增进读取速度：ldconfig
  - 判断可执行binary文件里面含有哪些动态函式库：ldd

- 检查软件的正确性

  - 检查文件的指纹码
  - 可以为重要的文件建立指纹数据库（例如/etc/shadow），然后写脚本定期检查指纹表是不是变化了

# 第二十二章 软件安装RPM，SRPM与YUM

![image-20230221213050454](/../img/image-20230221213050454.png)

![image-20230221213117775](/../img/image-20230221213117775.png)

- RPM有软件属性相依的问题，所以出现了YUM机制
- 现在主要利用rpm来做查询

![image-20230221213155747](/../img/image-20230221213155747.png)

![image-20230221213326856](/../img/image-20230221213326856.png)