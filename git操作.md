# 版本控制

本地版本控制

集中版本控制 SVN

历史版本都在服务器上，所以得定期保存

分布式版本管理系统

![image-20221019200314112](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019200314112.png)

每个人都有全部的代码

离线本地提交，联网时push。

# Git配置

```
git config --global user.name  "任炜"
git config --global user.email "2783800599@qq.com"
```

# Git基本理论

![image-20221019202651485](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019202651485.png)





![image-20221019203426033](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019203426033.png)





![image-20221019203542964](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019203542964.png)



```
# 初始化项目
git init
# 下载项目
git clone url 
```



# Git文件操作

![image-20221019204136276](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019204136276.png)



``` 
# 添加文件到暂存区
git add . 
# 提交暂存区内容到本地仓库 -m 提交信息
git commit -m "new file hello.txt"


# 忽略文件 idea中有.gitignore 规则如下
*.txt # 忽略所有.txt结尾的文件。
!lib.txt # 但lib.txt除外
/temp # 仅忽略根目录下的temp文件，子目录的TODO不会被忽略
build/ # 忽略build/目录下的所有文件
doc/*.txt # l


# 本机免密码登录 gitee
SSH 公钥 : xxxxxxxxxxxxxxxxx
# gitbash 生成公钥
ssh-keygen -t rsa
# 将公钥注册到码云中(本地的C .ssh/id_rsa.pub) 得到密钥





```

![image-20221019211024765](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019211024765.png)

# Git分支

![image-20221019212450983](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019212450983.png)

![image-20221019212535956](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019212535956.png)



```
# git 常用分支命令
git branch # 查看本地分支
git branch -r # 列出所有远程分支 -remote
git branch dev # 新建了分支
git checkout -b [branch] # 切换到某个分支
不要直接push，push前先 fetch下远程代码，如果有冲突，先fetch本地 到本地，看了哪里不一样，然后再push

在dev上操作，然后搞好后merge到主分支上
```

![image-20221019213853980](C:\Users\MSi\AppData\Roaming\Typora\typora-user-images\image-20221019213853980.png)
