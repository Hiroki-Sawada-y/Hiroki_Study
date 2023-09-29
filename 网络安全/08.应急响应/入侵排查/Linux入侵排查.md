## 用户

### 基本使用
```
1、用户信息文件/etc/passwd
root:x:0:0:root:/root:/bin/bash
account:password:UID:GID:GECOS:directory:shell
用户名：密码：用户ID：组ID：用户说明：家目录：登陆之后shell
注意：无密码只允许本机登陆，远程不允许登陆

2、影子文件/etc/shadow
root:$6$oGs1PqhL2p3ZetrE$X7o7bzoouHQVSEmSgsYN5UD4.kMHx6qgbTqwNVC5oOAouXvcjQSt.Ft7ql1WpkopY0UV9ajBwUt1DpYxTCVvI/:16809:0:99999:7:::
用户名：加密密码：密码最后一次修改日期：两次密码的修改时间间隔：密码有效期：密码修改到期到的警告天数：密码过期之后的宽限天数：账号失效时间：保留

```

```bash
who 查看当前用户(tty 本地登录 pts 远程登陆 )

w查看系统信息 想知道某一时刻用户的行为

uptime 查看登录多久 多少用户 负载
```

### 入侵排查
#### 查看用户
```
	awk -F: '$3==0{print $1}' /etc/passwd  查询特权用户特权用户(uid 为0)，默认只有root
	more /etc/sudoers | grep -v "^#\|^$" | grep "ALL=(ALL)"  查看拥有sudo权限的用户
	cat /etc/passwd | grep x:0           查看哪些用户为root权限
	cat /ect/passwd | grep -v nologin    查看除了不可登录以外的用户都有哪些
	cat /etc/passwd | grep /bin/bash     查看哪些用户使用shell
	awk '/\$1|\$6/{print $1}' /etc/shadow  查询可以远程登录的帐号信息
```

#### 禁用或删除账号
```
    usermod -L user     禁用帐号，帐号无法登录，/etc/shadow第二栏为!开头
    userdel user        删除user用户
    passwd -d username  删除username账号
    userdel -r user     将删除user用户，并且将/home目录下的user目录一并删除
```

#### 查看文件修改时间
`stat /etc/passwd `  查看密码文件上一次修改时间，最近被修改就有可能存在问题


## 历史命令
### 基本使用
```bash
通过.bash_history查看帐号执行过的系统命令
1、root的历史命令
histroy
2、打开/home各帐号目录下的.bash_history，查看普通帐号的历史命令

为历史的命令增加登录的IP地址、执行命令时间等信息：
1）保存1万条命令
sed -i 's/^HISTSIZE=1000/HISTSIZE=10000/g' /etc/profile
2）在/etc/profile的文件尾部添加如下行数配置信息：
######jiagu history xianshi#########
USER_IP=`who -u am i 2>/dev/null | awk '{print $NF}' | sed -e 's/[()]//g'`
if [ "$USER_IP" = "" ]
then
USER_IP=`hostname`
fi
export HISTTIMEFORMAT="%F %T $USER_IP `whoami` "
shopt -s histappend
export PROMPT_COMMAND="history -a"
######### jiagu history xianshi ##########
3）source /etc/profile让配置生效

生成效果： 1  2018-07-10 19:45:39 192.168.204.1 root source /etc/profile

3、历史操作命令的清除：history -c
但此命令并不会清除保存在文件中的记录，因此需要手动删除.bash_profile文件中的记录。
```

### 入侵排查

