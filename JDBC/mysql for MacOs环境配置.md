# mysql for MacOS 配置方法

---

## Mysql安装

官网下载mysql的dmg版本，不赘述，下载后直接安装

🙋 注意保存设置后root用户的密码

## 在终端中添加mysql命令

☁️ 重点部分：

1. 在terminal中进入/usr/local/mysql/bin

   * 可以现在finder中确认该目录下是否存在mysql

2. 在terminal中执行以下命令，打开文件

   ```
   vim ~/.bash_profile
   ```

3. 按下i进入编辑模式，在最后一行添加mysql/bin的目录作为环境变量

4. 按下esc退出编辑模式，按下组合键“shift+：”开启命令行，输入“wq”回车，保存退出

5. 最后在terminal中执行如下命令：

   ```
   source ~/.bash_profile
   ```

## mysql命令的使用

在上述环境变量的设置后，即可在terminal中直接执行mysql语句

首先在terminal中执行如下语句连接mysql

```
mysql -u root -p
```

按要求输入密码，见welcome则连接成功

## 服务的启停

mysql的服务启停在系统偏好设置当中进行设置