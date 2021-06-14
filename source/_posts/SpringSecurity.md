---
title: SpringSecurity单点登录
subtitle: 基于SpringSecurity的单点登录
date: 2020-06-19
author: 高明
tags:
	- Spring
---



# SpringSecurity单点登录

## 1 SpringSecurity

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

只要加入依赖，项目中的所有的接口都会被自动保护起来

```java
@RestController
public class helloController {
    @GetMapping("/hello")
    public String hello() {
        return "hello";
    }
}
```

当用户从浏览器发起请求

```
http://localhost:8080/hello
```

服务端会返回`302`响应码，让客户端重定向到`/login`页面，用户在`/login`登录成功后，就会自动跳转`/hello`接口。

```
用户名：user
密码：7b6a3aea-0d1a-4cef-bed7-185469b28490
```

默认情况下，登录的用户名是 `user` ，密码则是项目启动时随机生成的字符串，可以从启动的控制台日志中看到默认密码

这是随机生成的密码，每次启动都会变，对登录的用户名/密码进行配置，有三种方法

- 在 application.properties 中进行配置
- 通过 Java 代码配置在内存中
- 通过 Java 从数据库中加载

### 1.1 配置文件配置用户名/密码

```
spring.security.user.name=javaboy
spring.security.user.password=123
```

### 1.2 Java配置用户名/密码

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //下面这两行配置表示在内存中配置了两个用户
        auth.inMemoryAuthentication()
                .withUser("javaboy").roles("admin").password("$2a$10$OR3VSksVAmCzc.7WeaRPR.t0wyCsIj24k0Bne8iKWV1o.V9wsP8Xe")
                .and()
                .withUser("lisi").roles("user").password("$2a$10$p1H8iWa8I4.CA.7Z8bwLjes91ZpY.rYREGHQEInNtAp4NzL6PLKxi");
    }
    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

这里我们在 configure 方法中配置了两个用户，用户的密码都是加密之后的字符串(明文是 123)，从 Spring5 开始，强制要求密码要加密，如果非不想加密，可以使用一个过期的 PasswordEncoder 的实例 NoOpPasswordEncoder，但是不建议这么做，毕竟不安全。

Spring Security 中提供了 BCryptPasswordEncoder 密码编码工具，可以非常方便的实现密码的加密加盐，相同明文加密出来的结果总是不同，这样就不需要用户去额外保存`salt`的字段了，这一点比 Shiro 要方便很多

### 1.3 登录配置

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    VerifyCodeFilter verifyCodeFilter;
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.addFilterBefore(verifyCodeFilter, UsernamePasswordAuthenticationFilter.class);
        http
        .authorizeRequests()//开启登录配置
        .antMatchers("/hello").hasRole("admin")//表示访问 /hello 这个接口，需要具备 admin 这个角色
        .anyRequest().authenticated()//表示剩余的其他接口，登录之后就能访问
        .and()
        .formLogin()
        //定义登录页面，未登录时，访问一个需要登录之后才能访问的接口，会自动跳转到该页面
        .loginPage("/login_p")
        //登录处理接口
        .loginProcessingUrl("/doLogin")
        //定义登录时，用户名的 key，默认为 username
        .usernameParameter("uname")
        //定义登录时，用户密码的 key，默认为 password
        .passwordParameter("passwd")
        //登录成功的处理器
        .successHandler(new AuthenticationSuccessHandler() {
            @Override
            public void onAuthenticationSuccess(HttpServletRequest req, HttpServletResponse resp, Authentication authentication) throws IOException, ServletException {
                    resp.setContentType("application/json;charset=utf-8");
                    PrintWriter out = resp.getWriter();
                    out.write("success");
                    out.flush();
                }
            })
            .failureHandler(new AuthenticationFailureHandler() {
                @Override
                public void onAuthenticationFailure(HttpServletRequest req, HttpServletResponse resp, AuthenticationException exception) throws IOException, ServletException {
                    resp.setContentType("application/json;charset=utf-8");
                    PrintWriter out = resp.getWriter();
                    out.write("fail");
                    out.flush();
                }
            })
            .permitAll()//和表单登录相关的接口统统都直接通过
            .and()
            .logout()
            .logoutUrl("/logout")
            .logoutSuccessHandler(new LogoutSuccessHandler() {
                @Override
                public void onLogoutSuccess(HttpServletRequest req, HttpServletResponse resp, Authentication authentication) throws IOException, ServletException {
                    resp.setContentType("application/json;charset=utf-8");
                    PrintWriter out = resp.getWriter();
                    out.write("logout success");
                    out.flush();
                }
            })
            .permitAll()
            .and()
            .httpBasic()
            .and()
            .csrf().disable();
    }
}
```

我们可以在 successHandler 方法中，配置登录成功的回调，如果是前后端分离开发的话，登录成功后返回 JSON 即可，同理，failureHandler 方法中配置登录失败的回调，logoutSuccessHandler 中则配置注销成功的回调。

### 1.4 忽略拦截

如果某一个请求地址不需要拦截的话，有两种方式实现：

- 设置该地址匿名访问
- 直接过滤掉该地址，即该地址不走 Spring Security 过滤器链

推荐使用第二种方案，配置如下:

```Java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/vercode");
    }
}
```

## sso (快速)

### SecurityConfig.java

`SpringSecurity`配置类，需要实现`WebSecurityConfigurerAdapter`接口

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin() // 配置表单登录
                .loginProcessingUrl("/login/userLogin") // 表单登陆地址 post请求
                .and()
                .authorizeRequests() // 分别配置不同的权限属性
                .antMatchers("/login/**").permitAll() // 设置不进行权限验证的请求或资源 （登录请求不验证）
                .anyRequest().authenticated() // 其他请求需要登录才可以访问
            	.and()
            	.cors() // 设置跨域
            	.and()
            	.csrf().disable(); // 取消跨站请求伪造防护
    }
}
```

### UserAuthenticationProvider.java

`UserAuthenticationProvider`实现`AuthenticationProvider`接口

获取表单的用户名和密码，获取用户，进行比较，需要实现`loadUserByUsername`方法

```java
@Component
public class UserAuthenticationProvider implements AuthenticationProvider {
    @Autowired
    UserInfo userInfo;
    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        // 获取表单输入中返回的用户名
        String userName = (String) authentication.getPrincipal();
        // 获取表单中输入的密码
        String password = (String) authentication.getCredentials();
        // 查询用户是否存在
        UserDetails userDetails = userInfo.loadUserByUsername(userName); // 返回真实用户信息
        
        // 进行登录
        Set<GrantedAuthority> authorities = new HashSet<>();
        return new UsernamePasswordAuthenticationToken(userDetails, password, authorities);
    }
    @Override
    public boolean supports(Class<?> authentication) {
        return true;
    }
}
```

### UserService.java

`UserService`需要实现`UserDetailsService`接口，重写`loadUserByUsername`方法，返回真实的用户信息（从DB中）

```java
@Component
public class UserService implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        String pwd = "123456";
        String cryptPwd = new BCryptPasswordEncoder().encode(pwd);
        System.out.println("登录成功");
        return new User("s", cryptPwd, AuthorityUtils.commaSeparatedStringToAuthorityList("admin"));
    }
}
```

## sso

### 登录成功

向客户端返回`token`

```java
@Slf4j
@Component
public class UserLoginSuccessHandler implements AuthenticationSuccessHandler {
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication){
        // 组装JWT
        SelfUserEntity selfUserEntity =  (SelfUserEntity) authentication.getPrincipal();
        String token = JWTTokenUtil.createAccessToken(selfUserEntity);
        token = JWTConfig.tokenPrefix + token;
        // 封装返回参数
        Map<String,Object> resultData = new HashMap<>();
        resultData.put("code","200");
        resultData.put("msg", "登录成功");
        resultData.put("token",token);
        ResultUtil.responseJson(response,resultData);
    }
}
```

### 登录失败

需要实现`AuthenticationFailureHandler`接口

```java
@Slf4j
@Component
public class UserLoginFailureHandler implements AuthenticationFailureHandler {

    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception){
        // 这些对于操作的处理类可以根据不同异常进行不同处理
        if (exception instanceof UsernameNotFoundException){
            log.info("【登录失败】"+exception.getMessage());
            ResultUtil.responseJson(response,ResultUtil.resultCode(500,"用户名不存在"));
        }
        if (exception instanceof LockedException){
            log.info("【登录失败】"+exception.getMessage());
            ResultUtil.responseJson(response,ResultUtil.resultCode(500,"用户被冻结"));
        }
        if (exception instanceof BadCredentialsException){
            log.info("【登录失败】"+exception.getMessage());
            ResultUtil.responseJson(response,ResultUtil.resultCode(500,"用户名密码不正确"));
        }
        ResultUtil.responseJson(response,ResultUtil.resultCode(500,"登录失败"));
    }
}
```

### 登出成功

登出成功需要实现`LogoutSuccessHandler`接口

```Java
@Component
public class UserLogoutSuccessHandler implements LogoutSuccessHandler {

    @Override
    public void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication){
        Map<String,Object> resultData = new HashMap<>();
        resultData.put("code","200");
        resultData.put("msg", "登出成功");
        SecurityContextHolder.clearContext();
        ResultUtil.responseJson(response,ResultUtil.resultSuccess(resultData));
    }
}
```

### 未登录

未登录需要实现`AuthenticationEntryPoint`接口

```Java
@Component
public class UserAuthenticationEntryPointHandler implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception){
        ResultUtil.responseJson(response,ResultUtil.resultCode(401,"未登录"));
    }
}
```

### 未授权

未授权需要实现`AccessDeniedHandler`接口

```Java
@Component
public class UserAuthAccessDeniedHandler implements AccessDeniedHandler{

    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException exception){
        ResultUtil.responseJson(response,ResultUtil.resultCode(403,"未授权"));
    }
}
```

### 外部请求的过滤器

当登录成功之后，浏览器`cookie`中携带`token`向服务器发出请求，服务器需要拦截`token`并判断是否合法

```Java
@Slf4j
public class JWTAuthenticationTokenFilter extends BasicAuthenticationFilter {

    public JWTAuthenticationTokenFilter(AuthenticationManager authenticationManager) {
        super(authenticationManager);
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        // 获取请求头中JWT的Token
        String tokenHeader = request.getHeader(JWTConfig.tokenHeader);
        if (null!=tokenHeader && tokenHeader.startsWith(JWTConfig.tokenPrefix)) {
            try {
                // 截取JWT前缀
                String token = tokenHeader.replace(JWTConfig.tokenPrefix, "");
                // 解析JWT
                Claims claims = Jwts.parser()
                        .setSigningKey(JWTConfig.secret)
                        .parseClaimsJws(token)
                        .getBody();
                // 获取用户名
                String username = claims.getSubject();
                String userId=claims.getId();
                if(!StringUtils.isEmpty(username)&&!StringUtils.isEmpty(userId)) {
                    // 获取角色
                    List<GrantedAuthority> authorities = new ArrayList<>();
                    String authority = claims.get("authorities").toString();
                    if(!StringUtils.isEmpty(authority)){
                        List<Map<String,String>> authorityMap = JSONObject.parseObject(authority, List.class);
                        for(Map<String,String> role : authorityMap){
                            if(!StringUtils.isEmpty(role)) {
                                authorities.add(new SimpleGrantedAuthority(role.get("authority")));
                            }
                        }
                    }
                    //组装参数
                    SelfUserEntity selfUserEntity = new SelfUserEntity();
                    selfUserEntity.setUsername(claims.getSubject());
                    selfUserEntity.setUserId(Long.parseLong(claims.getId()));
                    selfUserEntity.setAuthorities(authorities);
                    UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(selfUserEntity, userId, authorities);
                    SecurityContextHolder.getContext().setAuthentication(authentication);
                }
            } catch (ExpiredJwtException e){
                log.info("Token过期");
            } catch (Exception e) {
                log.info("Token无效");
            }
        }
        filterChain.doFilter(request, response);
    }
```

```java
// 可以设置在request中设置context，这样每一个请求可以用@ApiIgnore Context context接收
request.setAttribute("context", new Context(
                    Integer.valueOf(signedJWT.getJWTClaimsSet().getJWTID()),
                    signedJWT.getJWTClaimsSet().getIssuer(),
                    salesType.getCode(),
                    signedJWT.getJWTClaimsSet().getIntegerClaim(TokenUtil.PROXY_ID),
                    signedJWT.getJWTClaimsSet().getStringClaim(TokenUtil.PROXY_NAME),
                    signedJWT.getJWTClaimsSet().getIntegerClaim(TokenUtil.TYPE)
            ));
```

### Token生成工具

```java
@Slf4j
public class JWTTokenUtil {

    /**
     * 私有化构造器
     */
    private JWTTokenUtil(){}

    public static String createAccessToken(SelfUserEntity selfUserEntity){
        // 登陆成功生成JWT
        String token = Jwts.builder()
                // 放入用户名和用户ID
                .setId(selfUserEntity.getUserId()+"")
                // 主题
                .setSubject(selfUserEntity.getUsername())
                // 签发时间
                .setIssuedAt(new Date())
                // 签发者
                .setIssuer("sans")
                // 自定义属性 放入用户拥有权限
                .claim("authorities", JSON.toJSONString(selfUserEntity.getAuthorities()))
                // 失效时间
                .setExpiration(new Date(System.currentTimeMillis() + JWTConfig.expiration))
                // 签名算法和密钥
                .signWith(SignatureAlgorithm.HS512, JWTConfig.secret)
                .compact();
        return token;
    }
}
```

### 获取用户信息

```Java
SecurityContextHolder.getContext().getAuthentication() .getPrincipal();
```

```Java
public class SecurityUtil {

    /**
     * 私有化构造器
     */
    private SecurityUtil(){}

    /**
     * 获取当前用户信息
     */
    public static SelfUserEntity getUserInfo(){
        SelfUserEntity userDetails = (SelfUserEntity) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
        return userDetails;
    }
    /**
     * 获取当前用户ID
     */
    public static Long getUserId(){
        return getUserInfo().getUserId();
    }
    /**
     * 获取当前用户账号
     */
    public static String getUserName(){
        return getUserInfo().getUsername();
    }
}
```

### response写数据

```Java
public static void responseJson(ServletResponse response, Map<String, Object> resultMap){
    PrintWriter out = null;
    try {
        response.setCharacterEncoding("UTF-8");
        response.setContentType("application/json");
        out = response.getWriter();
        out.println(JSON.toJSONString(resultMap));
    } catch (Exception e) {
        log.error("【JSON输出异常】"+e);
    }finally{
        if(out!=null){
            out.flush();
            out.close();
        }
    }
}
```

### 权限注解验证

需要实现接口

```Java
@Component
public class UserPermissionEvaluator implements PermissionEvaluator {
    @Autowired
    private SysUserService sysUserService;
    @Override
    public boolean hasPermission(Authentication authentication, Object targetUrl, Object permission) {
        // 获取用户信息
        SelfUserEntity selfUserEntity =(SelfUserEntity) authentication.getPrincipal();
        // 查询用户权限(这里可以将权限放入缓存中提升效率)
        Set<String> permissions = new HashSet<>();
        List<SysMenuEntity> sysMenuEntityList = sysUserService.selectSysMenuByUserId(selfUserEntity.getUserId());
        for (SysMenuEntity sysMenuEntity:sysMenuEntityList) {
            permissions.add(sysMenuEntity.getPermission());
        }
        // 权限对比
        if (permissions.contains(permission.toString())){
            return true;
        }
        return false;
    }
    @Override
    public boolean hasPermission(Authentication authentication, Serializable targetId, String targetType, Object permission) {
        return false;
    }
}
```

### Cotroller

```java
@PreAuthorize("hasRole('ADMIN')")
@RequestMapping(value = "/info",method = RequestMethod.GET)
public Map<String,Object> userLogin(){
    Map<String,Object> result = new HashMap<>();
    SelfUserEntity userDetails = SecurityUtil.getUserInfo();
    result.put("title","管理端信息");
    result.put("data",userDetails);
    return ResultUtil.resultSuccess(result);
}
```

```Java
/**
* 拥有sys:user:info权限可以访问
* hasPermission 第一个参数是请求路径 第二个参数是权限表达式
* @Author Sans
* @CreateTime 2019/10/2 14:22
* @Return Map<String,Object> 返回数据MAP
*/
@PreAuthorize("hasPermission('/admin/userList','sys:user:info')")
@RequestMapping(value = "/userList",method = RequestMethod.GET)
public Map<String,Object> userList(){
    Map<String,Object> result = new HashMap<>();
    List<SysUserEntity> sysUserEntityList = sysUserService.list();
    result.put("title","拥有sys:user:info权限都可以查看");
    result.put("data",sysUserEntityList);
    return ResultUtil.resultSuccess(result);
}
```

```java
@PreAuthorize("hasRole('ADMIN') and hasPermission('/admin/adminRoleList','sys:role:info')")
@RequestMapping(value = "/adminRoleList",method = RequestMethod.GET)
public Map<String,Object> adminRoleList(){
    Map<String,Object> result = new HashMap<>();
    List<SysRoleEntity> sysRoleEntityList = sysRoleService.list();
    result.put("title","拥有ADMIN角色和sys:role:info权限可以访问");
    result.put("data",sysRoleEntityList);
    return ResultUtil.resultSuccess(result);
}
```

