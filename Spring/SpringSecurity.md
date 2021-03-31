# SpringSecurity

Web应用的安全性包括用户认证(Authentication)和用户授权(Authorization)两个部分

```markdown
- 用户认证：验证某个用户是否为系统的合法主体，判断该用户能否访问该系统，用户认证一般要求用户提供用户名和密码，系统通过校验用户名和密码来完成认证过程
- 用户授权：验证某个用户是否有权限执行某个操作，一般系统为不同的用户分配不同的角色，而每个角色则对应一系列权限
```

```markdown
# SpringSecurity特点：
- 和 Spring 无缝整合
- 全面的权限控制
- 专门为 Web 开发而设计
- 重量级
# Shiro Apache 旗下的轻量级权限控制框架
- 轻量级
- 通用性，不局限于 Web 环境
```

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

# 基本原理

SpringSecurity 本质上是一个过滤器链

```markdown
# 典型过滤器：
* FilterSecurityInterceptor：是一个方法级别的权限过滤器，基本位于过滤链的最底部
* ExceptionTranslationFilter：异常过滤器，用来处理在认证授权过程中抛出的异常
* UsernamePasswordAuthenticationFilter：对 /login 的 POST 请求做拦截，校验表单中用户名、密码
# 过滤器如何加载？
* Spring Boot 对于 Spring Security 提供了自动化配置方案
- 1.使用SpringSecurity配置过滤器
- - DelegatingFilterProxy.class
- - - initDelegate()
- - - - FilterChainProxy.class
- - - - - getFilters() 得到所有过滤器
# 两个重要接口
* UserDetailsService
- 当什么也没有配置时，账号密码是由 Spring Security 定义生成的，而实际项目中账号密码都是从数据库中查询出来的，所以我们要通过自定义逻辑控制认证逻辑
- 如果需要自定义逻辑时（查数据库），只需要实现 UserDetailsService 接口即可
- 查询数据库中用户名和密码的过程，返回一个User对象，这个User对象是安全框架提供的对象
* PasswordEncoder
- 数据加密的接口，用于返回User对象里面密码加密
```

# Web权限方案

## 设置用户名和密码

- 1.通过配置文件
- 2.通过配置类
- 3.自定义编写实现类

```properties
# 第一种方式,在application.properties 中配置
spring.security.user.name=admin
spring.security.user.password=admin
```

```java
// 第二种方式,配置类
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    // 该方法用来设置登录的用户名和密码
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 加密算法
        BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
        String encodePassword = passwordEncoder.encode("123");
        // 设置用户民,密码,角色
        auth.inMemoryAuthentication().withUser("admin").password(encodePassword).roles("admin");
    }

    // 创建加密对象
    @Bean
    PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

### 自定义实现类

在项目启动时,会优先寻找配置文件和配置类中的用户名和密码，如果没有找到，就会寻找**UserDetailsService**接口。

```markdown
- 1.创建配置类，设置使用**UserDetailsService**实现类
- 2.编写实现类，返回User对象，User对象有用户名密码和操作权限
```

```java
// 1.创建配置类
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Resource
    private UserDetailsService userDetailsService;
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }
    @Bean
    PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

}
// 2.自定义实现类
@Service
public class MyUserDetailsService implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        // 设置用户角色
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        return new User("admin", new BCryptPasswordEncoder().encode("123"), auths);
    }
}
```

```java
// 整合数据库
// 在 UserDetailsService 调用 Mapper 里面的方法查询数据库进行用户认证
@Service
public class MyUserDetailsService implements UserDetailsService {

    // 注入 mapper
    @Resource
    private MyUsersMapper myUsersMapper;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {

        // 调用mapper查询数据库
        MyUsers myUser = myUsersMapper.selectOne(new QueryWrapper<MyUsers>().eq("name", s));
        // 用户为空,认证失败
        if (myUser == null) {
            throw new UsernameNotFoundException("用户名不存在");
        }
        // 设置用户角色
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        // 返回User
        return new User(myUser.getUsername(),
                new BCryptPasswordEncoder().encode(myUser.getPassword()),
                auths);
    }
}
```

## 自定义登录界面

```java
// 自定义设置页面和不需要认证的页面
@Override
protected void configure(HttpSecurity http) throws Exception{
    http.formLogin()    // 自定义自己编写的登录页面
        .loginPage("/login.html") // 登录页面设置
        .loginProcessingUrl("/user/login") // 登录访问的路径
        .defaultSuccessUrl("/index") // 登录成功后跳转的路径
        .permitAll()
        .and()
        .authorizeRequests()    // 定义需要认证的路径
        .antMatchers(
        "/",
        "/hello",
        "/user/login")  // 这些路径不需要认证
        .permitAll()
        .anyRequest().authenticated()   // 任何请求认证
        .and().csrf().disable();    // 关闭csrf防护
}
```

## 权限控制

### hasAuthority

```markdown
# hasAuthority 方法
* 如果当前的主体具有某个指定的权限,则返回 true ,否则返回 false
```

```java
// 1.在配置类设置访问地址有哪些权限
// 当前登录用户具有 admin 权限才可访问路径
.antMatchers("/index").hasAuthority("admin")
```

```java
// 2.在 UserDetailsService ,把返回User对象设置权限
// 设置用户角色
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
```

### hasAnyAuthority

```markdown
- 如果当前的主体有任何提供的角色(给定的作为一个逗号分隔的字符串列表)的话,返回true
```

```java
// 配置类
// 具有 admin 或 manager 权限可访问
.antMatchers("/index").hasAnyAuthority("admin,manager")
```

### hasRole

```markdown
- 如果用户具备给定角色就允许访问,否则403
```

```java
// 查看源码发现,角色前会加一个 ROLE_
private static String hasRole(String role) {
    Assert.notNull(role, "role cannot be null");
    Assert.isTrue(!role.startsWith("ROLE_"), () -> {
        return "role should not start with 'ROLE_' since it is automatically inserted. Got '" + role + "'";
    });
    return "hasRole('ROLE_" + role + "')";
}
```

```java
// 配置类
.antMatchers("/index").hasRole("student")
// UserDetailsService 实现类 手动加上 ROLE_
List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("ROLE_student");
```

### hasAnyRole

```markdown
- 表示用户具备任何一个角色条件都可以访问
```

## 自定义403

```java
http.exceptionHandling().accessDeniedPage("/unAuth.html");
```

## 注解使用

```java
// @Secured:用户具有某个角色,可以访问该方法
// 1.配置类上开启注解
// @EnableGlobalMethodSecurity(securedEnabled=true)
// 2.在controller方法使用
@GetMapping("/secured")
@Secured({"ROLE_student","ROLE_teacher"})
public String secured(){
    return "hello,secured";
}

// @PreAuthorize:进入方法前的权限验证,可以将登录用户的roles参数传到方法中
// @EnableGlobalMethodSecurity(prePostEnabled = true)
@GetMapping("/Authorize")
@PreAuthorize("hasAnyAuthority('admin')")
public String Authorize(){
    return "hello,Authorize";
}

// @PostAuthorize:在方法执行后再进行权限验证,适合验证带有返回值的权限
@PostAuthorize("hasAnyAuthority('admin')")

// @PostFilter:权限验证之后对数据进行过滤
// controller返回数据时只会返回用户名为admin的数据
@PostFilter("filterObject.username == 'admin'")

// @PreFilter:进入控制器之前对数据进行过滤
// 向controller传入数据时过滤
```

## 用户注销

```java
http.logout()
    // 退出页面
    .logoutUrl("/logout")
    // 退出成功后跳转的页面
    .logoutSuccessUrl("/index")
    .permitAll();
```

## 自动登录

