title: 多个git账号的配置
date: 2017-01-23 11:17:08
tags: Git
category:
---

# 多个git账号的配置

## 有时候我们需要在同一台Mac电脑下上使用多个git账号，为了避免冲突，我们需要配置~/.ssh/config文件，作为程序员会有多个代码管理的库，现在用SVN比较少了，Git成为主流，但是在电脑下如何同时保存多个ssh账号呢，自己总结了一下步骤：

*注意：整个修改过程按照github的ssh-key为范例*

#### 1、用ssh-keygen命令生成一组新的id_rsa_github和id_rsa_github.pub
```
cd ~/.ssh

ssh-keygen -t rsa -C "new email"
```
当我们在终端输入完上面的命令，回车后会提示：
```
Enter file in which to save the key (/Users/dabao/.ssh/id_rsa):
```
现在就是生成一个新的pub，在**:** 后输入你想生成文件的新名字，例如github的：id_rsa_github,名字可以随便起，然后回车。
```
Enter passphrase (empty for no passphrase):
```
我感觉整个秘密可以不用输入，我一般就是github的密码，按回车，确认密码，然后就生成了新的key.

*这个时候会发现，在~/.ssh文件夹下会多出一个id_rsa_github和id_rsa_github.pub的文件*

#### 2、打开config文件，如果没有就生成一个!
>这里可以修改Host，Host只是一个标记而已，比如说我有两个github账号，这时候我就要生成不同的key，用Host起一个别名用来区分就好了

```
Host git-oschina
    HostName git.oschina.net
    User xxx(名字随便写)
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_oschina
Host git-github
    HostName github.com
    User xxx(名字随便写)
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github
```
#### 3、然后我们要把ssh添加到git里面
```
ssh-add ~/.ssh/id_rsa_github
```
#### 4、然后测试一下是否OK
```
ssh -T git@github.com
Hi xxx! You've successfully authenticated,but GitHub does not provide shell access.
```

#### 5、完成之后需要设置config
> 在自己git项目下，终端运行

```
git config --list
```
查看自己config配置，路径在**~/.gitconfig**里，可以用文本打开

> 这个地方强调一下

```
git config --global user.name "xxx"
git config --global user.email "xxx@qq.com"
```

这个如果你想全局都是这个你可以选择gloabl，反正我的**~/.gitconfig**里面是我公司的配置，可一个人选择配置

> 又或者在自己项目里进行单独配置

```
git config user.name "xxx"
git config user.email "xxx@qq.com"

```
这个设置会显示在你的提交信息里面，在自己项目的**.git**目录下，有个名为**config**的文件，如果找不到可以在终端输入**l -list**
