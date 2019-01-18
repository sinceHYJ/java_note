#### 1.定义变量

```linux
#!/bin/bash
youname=heyingjie
```

#### 2.使用变量
```shell
${youname}
```

建议加括号，这是为了明确，若不加括号，容易判断时其他变量。

#### 3.只读
```shell
#!/bin/bash
myUrl="http://www.google.com"
readonly myUrl
myUrl="http://www.runoob.com"
```

#### 4.删除变量

```shell
unset variable_name
```

#### 5.变量拼接
```shell
PATH=${PATH}:/home/test
```

#### 6.命令先执行
```shell
cd /lib/module/$(uname -r)/kernel 推荐使用
或者
cd /lib/module/`uname -r`/kernel
```
