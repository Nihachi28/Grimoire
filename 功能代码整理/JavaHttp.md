# Java发送Http请求

> 利用Java向企业微信服务器发送Http请求并返回结果
>
> 参考：
>
> [利用Java对企业微信发送请求](https://blog.csdn.net/qq_38974638/article/details/113246970)
>
> [企业微信发送应用消息Document](https://work.weixin.qq.com/api/doc/90001/90143/90372)
>
> [获取token时没有HttpMessageConverter怎么处理](https://www.technicalkeeda.com/spring-tutorials/could-not-extract-response-no-suitable-httpmessageconverter-found-for-response-type)



## dependency

```java
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>org.springframework.web</artifactId>
  <version>3.2.2.RELEASE</version>
</dependency>
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.12.4</version>
</dependency>
```





## Code

```java
public void sendWechatMessage(String chatContext, String toParty) throws Exception {
  //读取并设置参数
  ResourceBundle bundle = ResourceBundle.getBundle("AccessParam");
  Integer AgentId = Integer.valueOf(bundle.getString("AgentId"));
  String id = bundle.getString("id");
  String password = bundle.getString("password");
  String CONTENT_TYPE = "Content-Type";

  //获取token地址
  String ACCESS_TOKEN_URL = "https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid="+id
    +"&corpsecret="+password;

  //发送消息地址
  String CREATE_SESSION_URL = "https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=";

  //构建请求json参数
  String json = "{\"toparty\":\"" + toParty + "\", " +
    "\"msgtype\":\"text\"," +
    "\"agentid\":"+ AgentId.toString() + "," +
    "\"text\":{"+ "\"content\":" + "\"" + chatContext +"\""+ "}" +
    "}";

  System.out.println(json);

  //initToken
  RestTemplate restTemplate = new RestTemplate();
  MappingJackson2HttpMessageConverter mappingJackson2HttpMessageConverter = new MappingJackson2HttpMessageConverter();
  mappingJackson2HttpMessageConverter.setSupportedMediaTypes(Arrays.asList(MediaType.APPLICATION_JSON, MediaType.APPLICATION_OCTET_STREAM));
  restTemplate.getMessageConverters().add(mappingJackson2HttpMessageConverter);

  JSONObject jsonObject = restTemplate.getForObject(ACCESS_TOKEN_URL, JSONObject.class);
  String token = jsonObject.getString("access_token");    //token

  //action
  String action = CREATE_SESSION_URL + token;

  //getHttp
  URL url = null;
  HttpURLConnection http = null;
  url = new URL(action);
  http = (HttpURLConnection)url.openConnection();
  http.setRequestMethod("POST");
  http.setRequestProperty(CONTENT_TYPE,"application/json;charset=UTF-8");
  http.setDoOutput(true);
  http.setDoInput(true);
  System.setProperty("sun.net.client.defaultConnectTimeout","30000");
  System.setProperty("sun.net.client.defaultReadTimeout","30000");
  http.connect();

  //sendMessage
  OutputStream os = http.getOutputStream();
  os.write(json.getBytes("UTF-8"));
  InputStream is = http.getInputStream();
  int size = is.available();
  byte[] jsonBytes = new byte[size];
  is.read(jsonBytes);
  String result = new String(jsonBytes, "UTF-8");
  System.out.println(result);
  os.flush();
  os.close();
}
```

