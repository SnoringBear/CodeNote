# JWT详细讲解

## 一.JWT

### 1.什么是JWT

> JSON Web Token (JWT)是一个开放标准(RFC 7519) ，它定义了一种紧凑和自包含的方式， **`用于作为 JSON 对象在各方之间安全地传输信息`**。此信息可以进行验证和信任，因为它是经过数字签名的。JWT 可以使用机密(使用 HMAC 算法)或使用 RSA 或 ECDSA 的公钥/私钥对进行签名。
> 虽然可以对 JWT 进行加密，以便在各方之间提供保密性，但是我们将关注已签名的Token。签名Token可以验证其中包含的声明的完整性，而加密Token可以向其他方隐藏这些声明。当使用公钥/私钥对对令牌进行签名时，该签名还证明只有持有私钥的一方才是对其进行签名的一方( **`签名技术是保证传输的信息不被篡改,并不能保证信息传输的安全`** )。

官网地址:https://jwt.io/introduction

![在这里插入图片描述](image/cee84469bd554742aceb8fb38cea81d4.png)



### 2.JWT的结构

在其紧凑的形式中，JWT由以点(.)分隔的三个部分组成，它们是:

- Header
- Payload
- Signature

类似于xxxx.xxxx.xxxx格式,真实情况如下:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

并且你可以通过官网https://jwt.io/#debugger-io解析出三部分表示的信息( **`可使用 JWT.io Debugger 来解码、验证和生成 JWT`** ):



![在这里插入图片描述](image/98d3fc61aa364cefa3ec866275569377.png)



#### (1) Header

> 报头通常由两部分组成: Token的类型(即 JWT)和所使用的签名算法(如 HMAC SHA256或 RSA)。

例如:

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

最终这个 JSON 将由base64进行加密（该加密是可以对称解密的)，用于构成 JWT 的第一部分,eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9就是base64加密后的结果。

#### (2) Payload

> Token的第二部分是有效负载，其中包含声明。声明是关于实体(通常是用户)和其他数据的语句。有三种类型的声明: registered claims, public claims, and private claims。

例如:

```
{
  "sub": "1234567890",// 注册声明
  "name": "John Doe",// 公共声明
  "admin": true // 私有声明
}
```

这部分的声明也会通过base64进行加密,最终形成JWT的第二部分eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ



**registered claims(注册声明)**

> 这些是一组预定义的声明，它们 **`不是强制性的，而是推荐的`** ，以 **`提供一组有用的、可互操作的声明`** 。

例如:

- iss: jwt签发者
- sub: jwt所面向的用户
- aud: 接收jwt的一方
- exp: jwt的过期时间，这个过期时间必须要大于签发时间
- nbf: 定义在什么时间之前，该jwt都是不可用的.
- iat: jwt的签发时间
- jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击

**`注意:声明名称只有三个字符，因为 JWT 意味着是紧凑的。`**

**Public claims(公共的声明)**

> 使用 JWT 的人可以随意定义这些声明( **`可以自己声明一些有效信息如用户的id,name等,但是不要设置一些敏感信息,如密码`** )。但是为了避免冲突，应该在 JWT注册表中定义它们，或者将它们定义为包含抗冲突名称空间的 URI。

**Private claims(私人声明)**

> 这些是创建用于在同意使用它们的各方之间共享信息的习惯声明，既不是注册声明，也不是公开声明( **`私人声明是提供者和消费者所共同定义的声明`** )。

**`注意:对于已签名的Token，这些信息虽然受到保护，不会被篡改，但任何人都可以阅读。除非加密，否则不要将机密信息放在 JWT 的有效负载或头元素中。`**





#### (3) Signature

> 要创建Signature，您必须获取编码的标头（header）、编码的有效载荷(payload)、secret、标头中指定的算法，并对其进行签名。

例如，如果您想使用 HMAC SHA256算法，签名将按以下方式创建:

```
HMACSHA256(
  base64UrlEncode(header) + "." +base64UrlEncode(payload),
  secret
  )
```

上面的JSON将会通过HMACSHA256算法结合secret进行校验签名(私钥加密)，其中header和payload将通过base64UrlEncode()方法进行base64加密然后通过字符串拼接 **`"."`** 生成新字符串,最终生成JWT的第三部分SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

**`注意:secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了`**

#### (4) JWT的生成与解析

> JWT输出是三个由点分隔的 Base64-URL 字符串，这些字符串可以在 HTML 和 HTTP 环境中轻松传递，同时与基于 XML 的标准(如 SAML)相比更加紧凑。

下面显示了一个 JWT，该 JWT 对前一个头和有效负载进行了编码，并使用一个 secret 进行签名。

![在这里插入图片描述](image/fe7a3884df1443329a1f35b2a7ae838c.png)

真实情况,一般是在请求头里加入Authorization，并加上Bearer标注最后是JWT(格式:Authorization: Bearer **`<token>`**)：

![在这里插入图片描述](image/2aa1d619cd3c4dbcbc0c7eb587f678a1.png)

- 通过Java代码实现JWT的生成( **`使用的是JJWT框架`** )

先导入JJWT的依赖(JJWT是JWT的框架)

```java
        <!--JWT(Json Web Token)登录支持-->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.1</version>
        </dependency>
```

测试代码如下:

```java
public class JjwtTest {
    @Test
    public void generateToken() {
        // JWT头部分信息【Header】
        Map<String, Object> header = new HashMap<>();
        header.put("alg", "HS256");
        header.put("typ", "JWT");

        // 载核【Payload】
        Map<String, Object> payload = new HashMap<>();
        payload.put("sub", "1234567890");
        payload.put("name","John Doe");
        payload.put("admin",true);

        // 声明Token失效时间
        Calendar instance = Calendar.getInstance();
        instance.add(Calendar.SECOND,300);// 300s

        // 生成Token
        String token = Jwts.builder()
                .setHeader(header)// 设置Header
                .setClaims(payload) // 设置载核
                .setExpiration(instance.getTime())// 设置生效时间
                .signWith(SignatureAlgorithm.HS256,"secret") // 签名,这里采用私钥进行签名,不要泄露了自己的私钥信息
                .compact(); // 压缩生成xxx.xxx.xxx

        System.out.println(token);
    }
}
```

运行结果:

![在这里插入图片描述](image/63a9833e99cb485c8f8e0723169c7bdb.png)

通过官网进行解码:
![在这里插入图片描述](image/869fb875ced24ff789e8fa8c05a1c2ef.png)

- 通过Java代码实现JWT的解码( **`使用的是JJWT框架`** )

测试代码如下:

```java
    @Test
    public void getInfoByJwt() {
        // 生成的token
        String token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWUsImV4cCI6MTY2MzI5NzQzMX0.Ju5EzKBpUnuIRhDG1SU0NwMGsd9Jl_8YBcMM6PB2C20";
        // 解析head信息
        JwsHeader jwsHeader = Jwts
                .parser()
                .setSigningKey("secret")
                .parseClaimsJws(token)
                .getHeader();

        System.out.println(jwsHeader); // {typ=JWT, alg=HS256}
        System.out.println("typ:"+jwsHeader.get("typ"));

        // 解析Payload
     Claims claims =    Jwts
                .parser()
                .setSigningKey("secret")
                .parseClaimsJws(token)
                .getBody();
        System.out.println(claims);// {sub=1234567890, name=John Doe, admin=true, exp=1663297431}
        System.out.println("admin:"+claims.get("admin"));

        // 解析Signature
     String signature =    Jwts
                .parser()
                .setSigningKey("secret")
                .parseClaimsJws(token)
                .getSignature();
        System.out.println(signature); // Ju5EzKBpUnuIRhDG1SU0NwMGsd9Jl_8YBcMM6PB2C20
    }
```

运行结果:
![在这里插入图片描述](image/dfa2dede4fe64759979276cb6c0dba89.png)

- JWT工具类(**`使用的是JJWT框架`** )

当然在实际项目中一般会将上面的操作封装成工具类来使用如下( **`该项目是一个Spring Security+JWT的项目`** ):

应用配置文件application.yaml中加入如下配置:

```java
jwt:
  tokenHeader: Authorization #JWT存储的请求头
  secret: mall-admin-secret #JWT加解密使用的密钥【私钥】
  expiration: 604800 #JWT的超期限时间(60*60*24*7)
  tokenHead: 'Bearer '  #JWT负载中拿到开头
```

工具类代码如下:

```java
package com.dudu.mall.utils;

import cn.hutool.core.date.DateUtil;
import cn.hutool.core.util.StrUtil;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

/**
 * JwtToken生成的工具类
 * JWT token的格式：header.payload.signature
 * header的格式（算法、token的类型）：
 * {"alg": "HS512","typ": "JWT"}
 * payload的格式（用户名、创建时间、生成时间）：
 * {"sub":"wang","created":1489079981393,"exp":1489684781}
 * signature的生成算法：
 * HMACSHA512(base64UrlEncode(header) + "." +base64UrlEncode(payload),secret)
 */
public class JwtTokenUtil {
    private static final Logger LOGGER = LoggerFactory.getLogger(JwtTokenUtil.class);
    private static final String CLAIM_KEY_USERNAME = "sub";
    private static final String CLAIM_KEY_CREATED = "created";
    @Value("${jwt.secret}")
    private String secret;
    @Value("${jwt.expiration}")
    private Long expiration;
    @Value("${jwt.tokenHead}")
    private String tokenHead;

    /**
     * 根据负责生成JWT的token
     */
    private String generateToken(Map<String, Object> claims) {
        return Jwts.builder()
                .setClaims(claims)
                .setExpiration(generateExpirationDate())
                .signWith(SignatureAlgorithm.HS512, secret)
                .compact();
    }

    /**
     * 从token中获取JWT中的负载
     */
    private Claims getClaimsFromToken(String token) {
        Claims claims = null;
        try {
            claims = Jwts.parser()
                    .setSigningKey(secret)
                    .parseClaimsJws(token)
                    .getBody();
        } catch (Exception e) {
            LOGGER.info("JWT格式验证失败:{}", token);
        }
        return claims;
    }

    /**
     * 生成token的过期时间
     */
    private Date generateExpirationDate() {
        return new Date(System.currentTimeMillis() + expiration * 1000);
    }

    /**
     * 从token中获取登录用户名
     */
    public String getUserNameFromToken(String token) {
        String username;
        try {
            Claims claims = getClaimsFromToken(token);
            username = claims.getSubject();
        } catch (Exception e) {
            username = null;
        }
        return username;
    }

    /**
     * 验证token是否还有效
     *
     * @param token       客户端传入的token
     * @param userDetails 从数据库中查询出来的用户信息
     */
    public boolean validateToken(String token, UserDetails userDetails) {
        String username = getUserNameFromToken(token);
        return username.equals(userDetails.getUsername()) && !isTokenExpired(token);
    }

    /**
     * 判断token是否已经失效
     */
    private boolean isTokenExpired(String token) {
        Date expiredDate = getExpiredDateFromToken(token);
        return expiredDate.before(new Date());
    }

    /**
     * 从token中获取过期时间
     */
    private Date getExpiredDateFromToken(String token) {
        Claims claims = getClaimsFromToken(token);
        return claims.getExpiration();
    }

    /**
     * 根据用户信息生成token
     */
    public String generateToken(UserDetails userDetails) {
        Map<String, Object> claims = new HashMap<>();
        claims.put(CLAIM_KEY_USERNAME, userDetails.getUsername());
        claims.put(CLAIM_KEY_CREATED, new Date());
        return generateToken(claims);
    }

    /**
     * 当原来的token没过期时是可以刷新的
     *
     * @param oldToken 带tokenHead的token
     */
    public String refreshHeadToken(String oldToken) {
        if(StrUtil.isEmpty(oldToken)){
            return null;
        }
        String token = oldToken.substring(tokenHead.length());
        if(StrUtil.isEmpty(token)){
            return null;
        }
        //token校验不通过
        Claims claims = getClaimsFromToken(token);
        if(claims==null){
            return null;
        }
        //如果token已经过期，不支持刷新
        if(isTokenExpired(token)){
            return null;
        }
        //如果token在30分钟之内刚刷新过，返回原token
        if(tokenRefreshJustBefore(token,30*60)){
            return token;
        }else{
            claims.put(CLAIM_KEY_CREATED, new Date());
            return generateToken(claims);
        }
    }

    /**
     * 判断token在指定时间内是否刚刚刷新过
     * @param token 原token
     * @param time 指定时间（秒）
     */
    private boolean tokenRefreshJustBefore(String token, int time) {
        Claims claims = getClaimsFromToken(token);
        Date created = claims.get(CLAIM_KEY_CREATED, Date.class);
        Date refreshDate = new Date();
        //刷新时间在创建时间的指定时间内
        if(refreshDate.after(created)&&refreshDate.before(DateUtil.offsetSecond(created,time))){
            return true;
        }
        return false;
    }
}
```

### 3.JWT是如何工作的

#### (1) JWT工作流程

下图显示了 JWT 是如何获得并用于访问 API 或资源的:
![在这里插入图片描述](image/ca38eb53d0294f74b70997d7d8506c52.png)

1. 应用程序或客户端向授权服务器请求授权。
2. 授予授权后，授权服务器将向应用程序返回访问令牌。
3. 应用程序使用访问令牌访问受保护的资源(如 API)。

#### (2) SpringBoot整合JWT案例

> 本项目JWT使用的是 **`JJWT框架`** 来实现,下图流程图来源于 [【编程不良人】JWT认证原理、流程整合springboot实战应用,前后端分离认证的解决方案!](https://www.bilibili.com/video/BV1i54y1m7cP?p=6&vd_source=5d242c523ffcdeae19faea79735ffef6),项目采用SpringBoot+mybatis实现,业务逻辑非常简单,下面截取关键代码来了解整个流程。

业务流程图如下:
![在这里插入图片描述](image/5da2a78200e248ada267791cfaf9b882.png)
登入功能通过业务层提供的login(user)方法进行登入,登入成功返回token信息,如果token信息不为null就将token信息返回给前端系统
![在这里插入图片描述](image/4e333712ae41434589e0814f67b7da0a.png)
业务层login(User user)方法实现逻辑非常简单,就是通过查询数据库中是否存在该用户,存在表示登入成功,并通过Token工具类生成token
![在这里插入图片描述](image/8ca8c7e43c5f4d87b296c6b62c0ee2de.png)
JWT工具类中generateToken(User user)虽然接受的是user对象,但是实际只使用了user的name作为载荷( **`不要将敏感信息写入到Token中`** )
![在这里插入图片描述](image/5f6782fcf9044248a800be6bd3e628d1.png)
![在这里插入图片描述](image/b4b0b7ebdb084f2599cffe8e5a90b120.png)

Postman中模拟前端访问[http://localhost](http://localhost/):8086/user/login?name=test&password=123456:
![在这里插入图片描述](image/efeb951d2e0b491498ec1899da3bcea9.png)

执行成功后前端会接受到服务器端传递过来的Token信息
![在这里插入图片描述](image/75d211cd376a448aa495ea15a4b43974.png)
访问Api信息/资源,前端只需要将token信息保存到请求头中,发送请求到对应Api/资源即可:
![在这里插入图片描述](image/66e3a4d059264f55b5bad012dbe660d8.png)
Postman模拟前端访问:[http://localhost](http://localhost/):8086/user/info?id=1
![在这里插入图片描述](image/931160fd76a041989b5ca71aba719e25.png)
至于Token的验证交给拦截器进行处理:

![在这里插入图片描述](image/54fb23a7ccdd4a189ae3ad02aeb319a3.png)
Postman访问/info:

- 访问成功

![在这里插入图片描述](image/69e9da2dfb0e4d4090dbcdc9bbdbeb1d.png)

- 访问失败

![在这里插入图片描述](image/4cb7753d717d4702840c11c026121fdf.png)

### 4.JWT的使用

> 📢下面的内容借鉴博文: https://zhuanlan.zhihu.com/p/66037342,该博文详细的介绍了单点登入相关知识,借鉴博文 [什么是 JWT -- JSON WEB TOKEN](https://www.jianshu.com/p/576dbf44b2ae?u_atoken=91172cd8-ee38-4f00-bd87-5dab015b84f6&u_asession=01Q62IwvW6pph_Ncb2bPTfJTtcPIIDd_JBDPweKSj23X4YV8vLFItAzGVhyzguKl8_X0KNBwm7Lovlpxjd_P_q4JsKWYrT3W_NKPr8w6oU7K88eJ9fBntafi4EfbpIWArOS_nntv0fnCqKMMYW1x5qJGBkFo3NEHBv0PZUm6pbxQU&u_asig=05VwcfgbreX6hu5gaiqbdbQPvTtZl3IjQykoynrX1-4ExH5Ei0IDhjyv82ZyZE9wTBqdHkfwlUM1djsumiL9sKCbyFJjrbA7vd09MjD-P1ZOAx6cYJ27w4jv5eIwPQDzPNg6zhtt9JS9Y2jr8XricVeh4fFGLUdkWD1st6FbkSzcX9JS7q8ZD7Xtz2Ly-b0kmuyAKRFSVJkkdwVUnyHAIJzdXCcbaxhcYEdIoWbAvQAIUFfrsZmVP-RYvrDdSkFZgr4Nx7nJyT20ni5onZOBbfUe3h9VXwMyh6PgyDIVSG1W9HpIJPqVKGPRDUkkOkQzbkjwQ0iZx7g0iaRIPGdTGFH-NI70t7RyxF--pNyQek64UfCt-d4M_yJ8xADtgch1kemWspDxyAEEo4kbsryBKb9Q&u_aref=W%2BMkoYG9XQQ5tQ52WnFs4GpiT%2BY=),该篇博文详细的介绍了JWT相关知识。

#### (1) Authorization(授权)

> 这是使用 JWT 最常见的场景。 **`一旦用户登录，每个后续请求都将包含 JWT，允许用户访问该Token所允许的路由、服务和资源`**。 **`单点登录`** 是目前广泛使用 JWT的一个特性，因为它的开销很小，而且 **`能够很容易地跨不同域使用`** 。

- **单系统**

> 单系统就是所有功能都在同一个系统上

![在这里插入图片描述](image/f4317c4f07ec49538f4abe826cfcc2d9.png)

- **多系统**

> 为了合理利用资源和降低耦合性，于是把单系统拆分成多个子系统（ **`传统基于session认证的登入设计是无法实现多系统登入的,因为session无法跨域`** ）

![在这里插入图片描述](image/a39a10404c5b462789cf65229c55bc14.png)

- **单点登入**

> 关于什么是单点登入,简单概述一下就是,在多个系统中，用户只需一次登录，各个系统即可感知该用户已经登录,例如淘宝和天猫这二个不同的子系统,当登入淘宝的时候,天猫自然而然就登入了。

- 单系统登入操作(基于session认证)

> Http是一种无状态的协议,意味着服务器无法确认用户信息,所以W3C推出Cookie来确认用户的信息,Cookie就相当于一个用户凭证,服务器可以通过这个凭证(Cookie)知道是哪一个用户发起了请求，虽然我们可以通过Cookie知道发起请求是谁,但是我们怎么知道这个用户是否已经发送过Cookie了呢,意思就是我们怎么知道某个用户是否已经登入了,这个时候就需要通过一张表来记录用户的信息,这就是Session，相当于在服务器中建立了一份“客户明细表”,Session无法知道发起的用户是谁,所以服务器为了确定用户的信息,会向服务器发送一个名为JESSIONID的Cookie，它的值是Session的id值,来确认用户的身份。

单系统登入常见的操作:

- 登入:将用户信息保存到Seesion对象中(Seesion中有用户信息表示用户已经登入,没有表示用户没有登入)
- 注销(退出登入): 将服务器中的Seesion对象移除
- 记住我:通过Cookie保存用户相关信息(这里的信息只能是字符串不能是对象,所以对象信息常常保存到数据库中),游览器关闭后任然还可以保持登入

业务代码如下:

```java
/**
 * 用户登陆
 */
@PostMapping(value = "/user/session", produces = {"application/json;charset=UTF-8"})
public Result login(String mobileNo, String password, String inputCaptcha, HttpSession session, HttpServletResponse response) {

    //判断验证码是否正确
    if (WebUtils.validateCaptcha(inputCaptcha, "captcha", session)) {

        //判断有没有该用户
        User user = userService.userLogin(mobileNo, password);
        if (user != null) {
            /*设置自动登陆，一个星期.  将token保存在数据库中*/
            String loginToken = WebUtils.md5(new Date().toString() + session.getId());
            user.setLoginToken(loginToken);
            User user1 = userService.userUpload(user);

            session.setAttribute("user", user1);

            CookieUtil.addCookie(response,"loginToken",loginToken,604800);

            return ResultUtil.success(user1);

        } else {
            return ResultUtil.error(ResultEnum.LOGIN_ERROR);
        }
    } else {
        return ResultUtil.error(ResultEnum.CAPTCHA_ERROR);
    }

}

/**
 * 用户退出
 */
@DeleteMapping(value = "/session", produces = {"application/json;charset=UTF-8"})
public Result logout(HttpSession session,HttpServletRequest request,HttpServletResponse response ) {

    //删除session和cookie
    session.removeAttribute("user");

    CookieUtil.clearCookie(request, response, "loginToken");

    return ResultUtil.success();
}
/**
* @author ozc
* @version 1.0
* <p>
* 拦截器；实现自动登陆功能
*/
public class UserInterceptor implements HandlerInterceptor {


@Autowired
private UserService userService;

public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object o) throws Exception {
    User sessionUser = (User) request.getSession().getAttribute("user");

    // 已经登陆了，放行
    if (sessionUser != null) {
        return true;
    } else {
        //得到带过来cookie是否存在
        String loginToken = CookieUtil.findCookieByName(request, "loginToken");
        if (StringUtils.isNotBlank(loginToken)) {
            //到数据库查询有没有该Cookie
            User user = userService.findUserByLoginToken(loginToken);
            if (user != null) {
                request.getSession().setAttribute("user", user);
                return true;
            } else {
                //没有该Cookie与之对应的用户(Cookie不匹配)
                CookieUtil.clearCookie(request, response, "loginToken");
                return false;
            }
        } else {

            //没有cookie、也没有登陆。是index请求获取用户信息，可以放行
            if (request.getRequestURI().contains("session")) {
                return true;
            }

            //没有cookie凭证
            response.sendRedirect("/login.html");
            return false;
        }
    }
}
}
```

上面的代码可以结合下图进行理解:
![在这里插入图片描述](image/2250dc07803a4c61a0012d75bf683684.png)

基于session认证的登入系统存在的问题:

- session

> 每个用户经过我们的应用认证之后，我们的应用都要在服务端做一次记录，以方便用户下次请求的鉴别，通常而言session都是保存在内存中，而随着认证用户的增多，服务端的开销会明显增大。

- 扩展性

> 用户认证之后，服务端做认证记录，如果认证的记录被保存在内存中的话，这意味着用户下次请求还必须要请求在这台服务器上,这样才能拿到授权的资源，这样在分布式的应用上，相应的限制了负载均衡器的能力。这也意味着限制了应用的扩展能力。

- CSRF

> 因为是基于cookie来进行用户识别的, cookie如果被截获，用户就会很容易受到跨站请求伪造的攻击。

基于token的鉴权机制,就没有这些问题:

- 基于token的鉴权机制

> 基于token的鉴权机制类似于http协议也是无状态的，它不需要在服务端去保留用户的认证信息或者会话信息。这就意味着基于token认证机制的应用不需要去考虑用户在哪一台服务器登录了，这就为应用的扩展提供了便利。

实际流程如下（ **`对应项目demo前面已经简述了,这里不在叙述`** ）:
![在这里插入图片描述](image/5da2a78200e248ada267791cfaf9b882.png)

#### (2) Information Exchange(信息交换)

> JWT是在各方之间安全传输信息的好方法。因为可以对 JWT 进行签名(例如，使用公钥/私钥对) ，所以可以确保发件人就是他们所说的那个人。此外，由于签名是使用头和有效负载计算的，因此还可以验证内容是否被篡改。

- 数字签名

> 数字签名就是发送方使用自己的私钥进行加密,接收方收到该签名后就可以使用发送方的公钥(公开的密钥,所有人都可以获取)进行解密,解密成功表示该消息没有被假冒且发送方也不能否认自己发送过。

### 5.为什么使用JWT

- 由于 JSON 没有 XML 那么冗长，所以当对它进行编码时，它的大小也更小，这使得 JWT 比 SAML 更加紧凑。这使得 JWT 成为在 HTML 和 HTTP 环境中传递的一个很好的选择。
- 在安全性方面，SWT 只能由使用 HMAC 算法的共享秘密对称签名。但是，JWT 和 SAML Token可以使用 X.509证书形式的公钥/私钥对进行签名。与签名 JSON 的简单性相比，使用 XML 数字签名,签名 XML 而不引入模糊的安全漏洞是非常困难的。
- JSON 解析器在大多数编程语言中都很常见，因为它们直接映射到对象。相反，XML 没有自然的文档到对象映射。这使得使用 JWT 比使用 SAML 断言更容易。
- 关于使用，JWT 是在 Internet 规模上使用的。这突出了在多个平台(尤其是移动平台)上对 JSON Web 令牌进行客户端处理的便利性。

下图就是JWT和SAML长度的比较:
![在这里插入图片描述](image/5db648e18d6a4ee1ba08c51588145efe.png)