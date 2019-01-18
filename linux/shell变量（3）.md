#### 1. login shell与no login shell

**login shell**取得bash需要完整的登陆流程，输入用户名、密码。
**no login shell** 不需要重复登录的举动，例如：在图形界面中打开终端，或者，通过bash命令打开（子进程）

#### 2. 两种情况读取的配置文件不一样
##### 2.1 login shell

a. 首先会读取/etc/profile，这是系统整体的设置，针对所有用户生效。
b. 其次会读取～/.bash_profile , ~/.bash_login, ~/.profile， **只会读取这三个文件中的1个，顺序如上所示，如果读取到某个文件，就不会再向下继续读取。

##### 2.2 no login shell

这种情况只会读取～/.bashrc这个文件，每种系统都不太一样，具体可以查看该文件。