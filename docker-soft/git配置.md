# git配置

## 一、设置用户名邮箱

$ git config --global user.name "yangsen"

$ git config --global user.email "[mr_yang_sen@163.com](mailto:349514537@qq.com)"

## 二、生成sshkey秘钥

```
ssh-keygen -t rsa -C "mr_yang_sen@163.com"
```

$ ssh -T [git@github.com](mailto:git@github.com)	Attempts to ssh to github

如果，看到：

```
Hi xxx! You've successfully authenticated, but GitHub does not # provide shell access.
```

恭喜你，你的设置已经成功了。

## 三、修改git的remote url

------

使用命令 git remote -v 查看你当前的 remote url

```
$ git remote -v
origin https://github.com/someaccount/someproject.git (fetch)
origin https://github.com/someaccount/someproject.git (push)
```

如果是以上的结果那么说明此项目是使用https协议进行访问的（如果地址是git开头则表示是git协议）

你可以登陆你的github，就像本文开头的图例，你在上面可以看到你的ssh协议相应的url，类似：

复制此ssh链接，然后使用命令 git remote set-url 来调整你的url。

```
git remote set-url origin git@github.com:someaccount/someproject.git
```

然后你可以再用命令 git remote -v 查看一下，url是否已经变成了ssh地址。

然后你就可以愉快的使用git fetch, git pull , git push，再也不用输入烦人的密码了