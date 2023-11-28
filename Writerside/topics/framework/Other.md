
# OAuth

1. 申请``client_id``和``client_secret``
2. 构建获取code的url (请求授权码)
   https://xxx.yyy.com/oauth-server/authorize?client_id=%s&response_type=code&redirect_uri=%s
3. 在浏览器访问这个url
   1. 未登录：跳转到sso平台。请求授权登录
   2. 已登录：直接进入回调url
4. 根据授权code获取accessToken (请求令牌)
   http://xxx.yyy.com/oauth-server/accessToken?client_id=CLIENT_ID&client_secret=SECRIT&grant_type=authorization_code&code=CODE&redirect_uri=REDIRECT_URI
5. 用accessToken获取userInfo
   https://xxx.yyy.com/oauth-server/userInfo?access_token=af9593e86f1d7d7618609a128749e6fe&code=e1b7c90a767b5900084a79b9aec11259

![oauth](oauth.jpg)

## DEMO
```java
public class Demo {
    @RequestMapping(value="codeCallback", method=RequestMethod.GET)
   public void codeCallback(HttpServletRequest request, HttpServletResponse response) {
        //回调中获取code
       String code = request.getParameter("code");
       ///构建获取accessToken的URL
       Map<String,String> headers = new HashMap<>();
       headers.put("Content-Type","application/x-www-form-urlencoded");

       Map res =  doPost(
               "https://xxx.yyy.com/oauth-server/accessToken?" +
                       "client_id="+clientId+"&" +
                       "client_secret="+clientSecret+"&" +
                       "grant_type=authorization_code&" +
                       "code="+code+"&" +
                       "redirect_uri=REDIRECT_URI",
               headers
       );
       //拿到access_token 和 expires_time过期时间
       String accessToken = res.getString("access_token");
       Integer expiresTime = res.getInteger("expires_time");

       //拿到access_token后 构建获取用户信息的URL
       String res2 =  doPost(
               "https://xxx.yyy.com/oauth-server/userInfo?" +
                       "access_token="+accessToken+"&" +
                       "code="+code,
               headers
       );
       String userId = res2.getString("userId");
       String userName = res2.getString("userName");
       String headImg = res2.getString("headImg");

       //获取到这些信息后 可以跳转回自己的首页
    }
}
```