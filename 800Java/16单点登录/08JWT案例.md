# JWT案例

- 依赖

  - ```xml
            <dependency>
                <groupId>com.auth0</groupId>
                <artifactId>java-jwt</artifactId>
                <version>3.19.2</version>
            </dependency>
            <dependency>
                <groupId>io.jsonwebtoken</groupId>
                <artifactId>jjwt</artifactId>
                <version>0.9.1</version>
            </dependency>
    ```

- 示例代码

  - ```java
    import com.alibaba.fastjson2.JSON;
    import io.jsonwebtoken.Claims;
    import io.jsonwebtoken.JwtBuilder;
    import io.jsonwebtoken.Jwts;
    import io.jsonwebtoken.SignatureAlgorithm;
    
    import java.nio.charset.StandardCharsets;
    import java.util.Date;
    import java.util.HashMap;
    import java.util.Map;
    
    public class JwtKit {
    
        public static void main(String[] args) {
    
            String username = "zhangsan";
    
            Date now = new Date();
            // jwt 有效期1小时
            long ttl = 60 * 1000L;
    
            Map<String, Object> subject = new HashMap<>();
            subject.put("username", username);
    //        String uuid = UUID.randomUUID().toString();
            String uuid = "6457ba87-776e-4b9c-afa9-b42278699073";
            System.out.println("uuid = " + uuid);
    
            System.out.println(genJwt(new Date(), ttl, subject, uuid));
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            String jwt = genJwt(new Date(), ttl, subject, uuid);
            System.out.println(jwt);
            Claims claims = parseJWT(jwt);
            System.out.println("claims = " + claims);
    
        }
    
        private static String genJwt(Date now, long ttl, Map<String, Object> subject, String uuid) {
            String secret = "123456";
            String iss = "sxt-test-jwt";
    
            JwtBuilder jwtBuilder = Jwts.builder();
            jwtBuilder.setId(uuid);
            jwtBuilder.setIssuer(iss); // 签发者
            jwtBuilder.setSubject(JSON.toJSONString(subject));
            jwtBuilder.setIssuedAt(now); // 签发时间
            jwtBuilder.signWith(SignatureAlgorithm.HS256, secret.getBytes(StandardCharsets.UTF_8));
            jwtBuilder.setExpiration(new Date(now.getTime() + ttl)); // 过期时间
            String jwtToken = jwtBuilder.compact();
            return jwtToken;
        }
    
        public static Claims parseJWT(String jwt) {
            String secret = "123456";
            return Jwts.parser()
                    .requireId("6457ba87-776e-4b9c-afa9-b42278699073")
                    .requireIssuer("sxt-test-jwt")
                    .requireSubject("{\"username\":\"zhangsan\"}")
                    .setSigningKey(secret.getBytes(StandardCharsets.UTF_8))
                    .parseClaimsJws(jwt)
                    .getBody();
        }
    }
    ```

- `Controller`逻辑
  - 登录
    - 签发`JWT`
  - 其他
    - 验证`JWT`
    - 重新签发`JWT`

