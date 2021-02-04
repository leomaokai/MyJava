# JWT

## JWT

JSON Web Token(JWT)是一个开放标准，他定义了一种紧凑的、自包含的方式，用于在各方之间以JSON对象安全传输信息，此信息可以验证和信任，因为他是数字签名的，jwt可以只用秘密（使用HMAC算法）或使用RSA或ECDSA的公钥/私钥对进行签名

JWT简称 JSON Web Token ，是通过JSON形式作为Web应用中的令牌，用在各方之间安全地将信息作为JSON对象传输，在数据传输过程中还可以完成数据加密、签名等相关处理。

可以用来信息交换，也可以在JavaWeb中做安全验证

功能：

* 授权：这是使用JWT的最常见方案，一旦用户登录，每个后续请求将包含JWT，从而允许用户访问该令牌允许的路由，服务和资源。单点登录是当今广泛使用JWT的一项功能，因为它的开销很小并且可以在不同的城中轻松使用。
* 信息交换：JSON Web Token 是在各方之间安全传输信息的好方法，因为JWT进行签名，所以可以确保发件人是他们所说的人，此外，由于签名是使用标头和有效负载计算的，因此还可以验证内容是否遭到篡改。

## Session问题

传统的session认证

![image-20210128001352575](Jwt.assets/image-20210128001352575.png)

问题：

* 每个用户经过我们应用认证后，我们的应用都要在服务端做一次记录，以便用户下次请求的鉴别，通常session保存在内存中，而随着认证用户的增多，服务端的开销会明显增大
* 用户认证之后，服务端做认证记录，如果认证的记录保存在内存中的话，这意味着用户下次请求还必须在这台服务器上，这样才能拿到授权的资源，这样在分布式应用中，相应的限制了负载均衡的能力，也意味着限制了应用的扩展能力
* 因为基于cookie来进行用户识别的，cookie如果被截获，用户很容易受到跨站请求伪造的攻击
* 不利于前后端分离
  * Session认证给前后端分离在应用解耦后增加了部署的复杂性，通常用户一次请求就要转发多次，如果用Session每次携带sessionid到服务器，服务器还要查询用户的信息。如果用户很多，这些信息存储在服务器内存中，给服务器增加负担。
  * 还存在CSRF（跨站伪造请求攻击）攻击，session是基于cookie进行用户识别的，cookie容易被截获，用户很容易受到跨站请求伪造的攻击
  * sessionid就是一个特征值，表达的信息不够丰富，不容易扩展，而且如果后端应用是多节点部署，那么就需要实现session共享机制，不方便集群应用

## 基于JWT认证

![image-20210128003947925](Jwt.assets/image-20210128003947925.png)

认证流程：

* 首先，前端通过Web表单将自己的用户名和密码发送到后端接口，这一过程一般是一个POST请求，通常使用HTTPS加密传输
* 后端核对用户名和密码成功后，将用户的id等其他信息作为JWT Payload（负载），将其与头部分别进行Base64编码接拼后签名，形成一个JWT(Token)，形成的JWT就是一个形同`lll.zzz.xxx`的字符串
* 后端JWT字符串作为登录成功的返回结果返回给前端，前端可以将返回的结果保存在 localStorage 或 sessionStorage 上，退出登录时前端删除保存的JWT即可
* 前端在每次请求时将JWT放入HTTP Header中的 Authorizaton 位（解决XSS和XSRF问题）
* 后端检查是否存在，如存在验证JWT的有效性，例如，检查签名是否正确；检查Token是否过期，检查Token的接收方是否是自己
* 验证通过后后端使用JWT中包含的用户信息进行其他逻辑操作，返回相应结果

JWT优势：

* 简洁(Compact)，可以通过URL，POST参数或者在 HTTP header 发送，因为数据量小，传输速度很快
* 自包含(Self-contained)，负载中包含了所有用户所需要的信息，避免了多次查询数据库
* 因为Token是以JSON加密的形式保存在客户端，所以JWT是跨语言的
* 不需要再服务端保存会话信息，特别适用于微服务

# JWT结构

```markdown
# 令牌组成
* 标头(Header)
* 有效负载(Payload)
* 签名(Signature)
* 所以,JWT通常的组成  Header.Payload.Signature
```

## Header

```markdown
# Header
* 标头通常由两部分组成,令牌的类型和所使用签名算法,例如HMAC SHA256或RSA,他会使用Base64编码组成JWT结构的第一部分
* Base64是一种编码,他是可以被翻译回原来的样子,并不是一种加密过程
​```json
{
	"alg":"HS256",
	"typ":"JWT"
{
​```
```

## Payload

令牌的第二部分是有效负载，其中包含声明（非敏感信息）。声明式有关实体和其他数据的声明，同样的，他会使用 Base64 编码组成JWT结构的第二部分

```json
{
	"sub":"123456",
	"name":"kai",
	"admin":true
}
```

## Signature

前面两部分都是使用 Base64 进行编码，即前端可以解开里面的信息。Signature 需要使用编码后的 header 和 payload 以及我们提供的一个密钥，然后使用 header 中指定的签名算法（HS256）进行签名，签名的作用保证JWT没有被篡改过

```
HMACSHA256( base64UrlEncode(header) . base64UrlEncode(payload) . secret )
```

签名目的：

* 最后一步签名过程，实际上是对头部以及负载内容进行签名，防止内容被篡改，如果有人对头部以及负载内容解码之后进行修改，再进行编码，最后加上之前的签名组合形成新的JWT的话，那么服务器端会判断出新的头部和负载形成的签名和JWT附带上的签名是不一样的。如果要对新的头部和负载进行签名，在不知道服务器加密时用的密钥的话，得出来的签名也是不一样的

# 使用JWT

## hello

引入依赖

```xml
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.4.0</version>
</dependency>
```

获取令牌

```java
@Test
public void test01() {

    Map<String, Object> map = new HashMap<>();

    Calendar instance = Calendar.getInstance();
    instance.add(Calendar.SECOND, 20);

    String token = JWT.create()
        .withHeader(map) //header，可以不写
        .withClaim("userId", 10) //payload
        .withClaim("username", "leo")
        .withExpiresAt(instance.getTime()) //令牌的过期时间
        .sign(Algorithm.HMAC256("!@#$%^"));//签名，常用此算法

    System.out.println(token);
}
//eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MTE4MTIwMjMsInVzZXJJZCI6MTAsInVzZXJuYW1lIjoibGVvIn0.ZebnJcbeShVSFOySsLkigwLRwP6N8tfhw4ARYNpaZuE
```

验证令牌

```java
@Test
public void test02(){
    //生成验证对象
    JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("!@#$%^")).build();

    //验证token,得到解码对象
    DecodedJWT verify = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MTE4MTIwMjMsInVzZXJJZCI6MTAsInVzZXJuYW1lIjoibGVvIn0.ZebnJcbeShVSFOySsLkigwLRwP6N8tfhw4ARYNpaZuE");

    //通过解码对象得到负载信息
    System.out.println(verify.getClaim("userId").asString());
    System.out.println(verify.getClaim("username").asString());
    System.out.println(verify.getClaims().get("userId").asInt());
    System.out.println(verify.getClaims().get("username").asString());
    System.out.println(verify.getClaims().get("username").asString());

}
//10
//leo
//10
//leo
//Thu Jan 28 13:33:43 CST 2021
```

异常

```
SignatureVerificationExpection	签名不一致异常
TokenExpiredException		令牌过期异常
AlgorithmMismatchException	算法不匹配异常
InvalidClaimException		失效的负载异常
```



## 工具类

```java
public class JWTUtils {

    private static final String SIGN = "!@#$%^";

    /**
     * 生成token header.payload.sign
     */
    public static String getToken(Map<String, String> map) {

        Calendar instance = Calendar.getInstance();
        instance.add(Calendar.DATE, 7);//默认7天过期

        //创建jwt builder
        JWTCreator.Builder builder = JWT.create();
        //payload
        map.forEach((k, v) -> {
            builder.withClaim(k, v);
        });

        //指定过期时间
        builder.withExpiresAt(instance.getTime());
        //sign
        String token = builder.sign(Algorithm.HMAC256(SIGN));

        return token;
    }

    /**
     * 验证token
     */
    public static DecodedJWT verify(String token) {
        return JWT.require(Algorithm.HMAC256(SIGN)).build().verify(token);
    }

//    /**
//     * 获取信息
//     */
//    public static DecodedJWT getTokenInfo(String token){
//        DecodedJWT verify = JWT.require(Algorithm.HMAC256(SIGN)).build().verify(token);
//        return verify;
//    }
}
```

