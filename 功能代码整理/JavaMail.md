# JavaMail

> 利用java发送邮件
>
> 参考：[Java发送邮件详解](https://blog.csdn.net/qq_41151659/article/details/96475739)



## dependency

```java
<dependency>
  <groupId>javax.mail</groupId>
  <artifactId>javax.mail-api</artifactId>
	<version>1.6.2</version>
</dependency>
<dependency>
  <groupId>com.sun.mail</groupId>
  <artifactId>javax.mail</artifactId>
  <version>1.6.2</version>
</dependency>
```



## Code

```java
/**
 * 方法sendMail传入：1.邮件内容、2.发送信箱、3.接收信箱、4.发送信箱授权码
 */

public void sendMail(String mailContext, final String fromMail, String toMail, final String authorCode) throws Exception{
    Properties prop = new Properties();
    prop.setProperty("mail.host","smtp.qq.com");    //设置QQ邮箱服务器
    prop.setProperty("mail.transport.protocol","smtp");     //邮件发送协议
    prop.setProperty("mail.smtp.auth","true");      //需要验证用户名密码

    //1.创建定义整个应用程序所需要的环境信息Session对象
    Session session = Session.getDefaultInstance(prop, new Authenticator() {
      @Override
      protected PasswordAuthentication getPasswordAuthentication() {
        return new PasswordAuthentication(fromMail,authorCode);
      }
    });

    //开启Session的debug模式
    session.setDebug(true);

    //2.通过session得到transport对象
    Transport ts = session.getTransport();

    //3.使用邮箱用户名和授权码连接服务器
    ts.connect("smtp.qq.com",fromMail,authorCode);

    //4.创建邮件
    //创建邮箱对象
    MimeMessage message = new MimeMessage(session);

    //指名邮件的发件人
    message.setFrom(new InternetAddress(fromMail));

    //指名邮件的收件人
    message.setRecipient(Message.RecipientType.TO,new InternetAddress(toMail));

    //邮件标题
    message.setSubject("告警邮件");

    //内容文本
    message.setContent(mailContext, "text/html;charset=UTF-8");

    //5.发送邮件
    ts.sendMessage(message,message.getAllRecipients());
    ts.close();
    }
```

