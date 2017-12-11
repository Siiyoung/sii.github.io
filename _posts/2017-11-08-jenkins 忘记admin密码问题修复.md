---
layout: post
title:  "2017-11-08-jenkins 忘记admin密码问题修复"
date:   2017-11-08 15:21:02 +0800
categories: 持續集成
tags: 持續集成 CI 配置 jenkins
author: Sii
---
 
* content
{:toc}


### 问题表现
* errMsg : 登录信息无效。请重试。 
* 提示信息： If you are a system administrator and suspect this to be a configuration problem, see the server console output for more details.

### 解决方案
#### 找回密码
1. 去到本地用户配置路径，查看配置文件
 ```
 C:\Users\[当前电脑登录用户名]\.jenkins\users\admin
 ```
 
对应的 ==<fullName>== 节点value即为当前登录用户名，默认为==admin==</p>
对应的 ==<passwordHash>== 节点value截取#jbcrypt:之后部分即为加密后的密码，密码无法解密，但是可以校验

2. 去到存储admin密码路径查看密码铭文，路径为：
```
C:\Users\[当前电脑登录用户名]\.jenkins\secrets\initialAdminPassword
```
3. 密码校验方法（可以没有）：
```
/**
     * jenkins密碼校驗方法
     * @param plaintext  密碼明文
     * @param hashed     密碼密文
     * @return           校驗結果（true:一致;false:不一致）
     */
    public boolean checkJenkinsPasswoed (String plaintext,String hashed) {
        if (hashed.equals(plaintext)) {
            return true;
        } else {
            return false;
        }
    }
```
jbcrypt对应的maven依赖
```
        <!-- Jenkins密码加解密hash依赖 -->
        <dependency>
            <groupId>org.mindrot</groupId>
            <artifactId>jbcrypt</artifactId>
            <version>0.3m</version>
        </dependency>
```

3. 登录即可

**若以上不能解决，重启jenkins即可，重启步骤如下：**

1. 杀掉对应的tomcat进程（windows）
   1. 查看端口对应的进程id，默认为8080，示例：
   ```
   netstat -aon| findstr "8080"  
   ```
   2. 根据进程id,杀死进程
   ```
    taskkill /F /PID [进程id]
   ```
   3. 重启tomcat，点击 startup.bat启动即可，路径格式为：
   ```
   E:\apache-tomcat-8.5.20\bin
   ```
2. 启动jenkins
   1. 在cmd窗口进入到本地jenkins安装目录
   2. 启动jenkins web服务，指令如下：
   ```
   java -jar jenkins.war --httpPort=8080
   ```
   


至此，顺利解决。


{% include comments.html %}


