# 1 命令介绍

sudo命令是linux的系统管理命令，可以让普通用户执行一些其他用户的(一般是root用户)特有的命令，不用切换用户，从而提高效率和易用性。

sudo工具提供了日志系统，详细记录了某个用户执行了哪些操作。sudo系统把需要管理的用户记录在一个叫做`sudoers`的文件里，里面记录了哪些用户，可以执行哪些命令的规则。当用户需要执行特权命令时，需要通过sudo的验证(一般是提供自己账户的密码)，从而sudo验证当前用户是否合法，如果合法就以sudo指定的用户身份，运行当前用户需要的命令进程。每次成功调用一次sudo命令，用户就获取了一个长达5分钟(默认值)的token，下次调用一些特有命令时，不需要再输入密码。

# 2 简单使用

常见选项解释：

1.  -V 显示版本
2. -h 显示帮助信息
3. -l 显示出当前执行sudo命令用户的权限
4. -k 需要认证当前用户身份，一般是输入密码
5. -b 后台执行命令
6. -p prompt 如果不指定的话，默认提示语为 [sudo] password for xxx ，这和选项是用于更改这个提示的，可以使用%u代表当前执行sudo命令的用户名，%h代表当前的主机名称，例如`-p %u----%p`
7. -u username 或者是#uid 代表以username的身份执行命令，如果不指定，默认认为root用户
8. -i 切换到root用户
9.  -S 从标准输入流替代终端来获取密码，可以使用管道符号，实现自动登录

# 3 相似命令 su

su命令相当于切换用户，在当前的会话中开启一个新的会话，登录到另一个用户，用法

`su [-] 用户名`，需要输入想要切换到的那个用户的密码，注意横杠与用户之间的空格

如果用户名不写，表示切换到root，其中横杠是可选的，如果加上，表示是相当于从登录窗口登录的一样，登陆后，家目录以及其他的环境变量都是切换之后的用户的，不加的话，只是登录账户变了，家目录和环境变量没有改变

和sudo的区别，su命令是切换用户账户，sudo命令做的是提权操作