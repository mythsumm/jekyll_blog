---
title: '在GitHub多个账号上添加SSH公钥'
layout: post
tags:
    - GitHub
    - SSh
    - 公钥
---
*GitHub后台可以添加多个SSH Keys，但是同一个SSH Keys只能在添加在一个帐号上（添加时提示“Key is already in use”）。SSH公钥使用时相当于用户名密码，不能两个不同的帐号使用同一个用户名密码。要想在多个GitHub帐号上添加公钥，就要在本地生成多个SSH Keys，每个GitHub帐号对应一个不同的SSH Keys。步骤如下：*

1. 生成一个新的SSH KEY。

    * `ssh-keygen -t rsa -C 'ethanliao924@gmail.com'`

    * `Enter file in which to save the key (~/.ssh/id_rsa): id_rsa2`

    * `ssh-add ~/.ssh/id_rsa2`

     如果出现Could not open a connection to your authentication agent的错误，就试着用以下命令：

     `ssh-agent bash`

     `ssh-add ~/.ssh/id_rsa_work`

2. 打开新生成的~/.ssh/id_rsa2.pub文件，将里面的内容添加到GitHub后台。

3. 打开~/.ssh/config文件（没有则创建），添加一个Host：

   *建一个github别名，新建的帐号使用这个别名做克隆和更新*
   ```
     Host github2
     
     HostName github.com
     
     User git
     
     IdentityFile ~/.ssh/id_rsa2.pub
   ```
4. 测试。

    `ssh -T git@github.com`

5. 应用，将GitHub SSH仓库地址中的git@github.com替换成新建的Host别名。

    *如原地址是：git@github.com:mythsumm/jekyll_blog.git，替换后应该是：github2:mythsumm/jekyll_blog.git
如果是新建的仓库，直接使用替换后的URL克隆即可。如果已经使用原地址克隆过了，可以使用命令修改：*

    `git remote set-url origin github2:freehost/mail.git`


