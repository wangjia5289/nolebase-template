# 一、理论 + [导图：Spring Security](../5、导图集合/导图：SpringSecurity.xmind)

### 1、Spring Security 概述

Spring Security 是一个专为基于 Spring 的应用程序提供保护的强大框架，专注于**加密**、**安全防御**、**认证**、**授权**以及**记住我**功能。

---


### 2、Spring Security 核心 API 

#### 2.1、UserDetailsSerevice

`UserDetailsService` 是 Spring Security 中的核心接口，负责获取用户身份信息并将其封装到 `UserDetails` 对象中。

Spring Security 提供了该接口的默认实现，但如果需要自定义 `UserDetails` 的信息来源，则通常需要自定义实现 `UserDetailsService`。自定义实现的定义如下：

我们通常只需要自定义一个 `UserDetailsService` 类（通常将其命名为：`CustomUserDetailsService`）并实现 `UserDetailsService` 接口，重写其 `loadUserByUsername` 方法，并将其[[笔记：加载用户信息的方式==#2.5、配置 `UserDetailsService` 到 Spring Security|配置到 Spring Security 中]]
```java
// CustomUserDetailsService.java

@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // 根据用户传来的用户名查询用户基本信息
        User user = userMapper.findUserByUsername(username);

        if (user == null) {
            throw new UsernameNotFoundException("用户名未找到: " + username);
        }

        // 查询用户的角色信息
        List<Role> roles = userMapper.findRolesByUserId(user.getId());

        // 转换角色为 GrantedAuthority 列表
        Collection<GrantedAuthority> authorities = roles.stream()
            .map(role -> new SimpleGrantedAuthority("ROLE_" + role.getName()))
            .collect(Collectors.toList());

        // 返回封装的 CustomUserDetails
        return new CustomUserDetails(user, authorities);
    }
}
```

---


#### 2.2、UserDetails

`UserDetails` 是 Spring Security 中用于封装用户详细信息的核心接口。当用户提供认证信息时，通常会通过 `UserDetailsService` 根据传入的用户名查询用户信息，并将其封装到 `UserDetails` 中。随后，系统通常会通过用户名和密码与用户提供的认证信息进行匹配（使用 `AuthenticationManager` 进行认证，默认情况下只使用用户名和密码。自定义认证则可以灵活选择）。认证成功后，用户信息会被封装到 `Authentication` 对象中，作为认证成功的结果。

我们通常只需要自定义一个 `UserDetails` 类（通常将其命名为：`CustomUserDetails`）并实现 `UserDetails` 接口，重写其中的七个方法。

==如果需要扩展 `UserDetails`，我们只需在 `UserDetails` 中添加新的方法==，如 `public String getEmail()`，并在其中指定这个数据来源何处。

<font color="#ff0000">需要注意，不建议将用户的所有信息，尤其是敏感信息，存储在 `UserDetails` 中，因为这样既不推荐也不安全。`UserDetails` 应仅用于存储非敏感信息。如果某些敏感信息必须存储在 `UserDetails` 中（例如，使用 `AuthenticationManager` 进行认证时，密码是必须存储在 `UserDetails` 中的），那么在认证完成后，将 `UserDetails` 封装为 `Authentication` 对象时，应确保将敏感信息（如密码）设置为 `null`，以避免其在后续操作中泄露。</font>

```
// 实现 UserDetails 接口

public class CustomUserDetails implements UserDetails {

    private User user; // 通过 UserDetailsService 获取到的 User 对象，用于访问用户相关信息
    private Collection<? extends GrantedAuthority> authorities; // 通过 UserDetailsService 获取到的权限列表，直接返回
    
    public CustomUserDetails(User user, Collection<? extends GrantedAuthority> authorities) {
        this.user = user;
        this.authorities = authorities;
    }

    // 获取用户的权限
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    // 获取密码
    @Override
    public String getPassword() {
        return user.getPassword();
    }

    // 获取用户名
    @Override
    public String getUsername() {
        return user.getUsername();
    }

    // 账户是否过期
    @Override
    public boolean isAccountNonExpired() {
        return !user.isAccountExpired();  // 返回数据库字段中的值，表示账户是否过期
    }

    // 账户是否被锁定
    @Override
    public boolean isAccountNonLocked() {
        return !user.isAccountLocked();  // 返回数据库字段中的值，表示账户是否被锁定
    }

    // 密码是否过期
    @Override
    public boolean isCredentialsNonExpired() {
        return !user.isCredentialsExpired();  // 返回数据库字段中的值，表示密码是否过期
    }

    // 用户是否启用
    @Override
    public boolean isEnabled() {
        return user.isEnabled();  // 返回数据库字段中的值，表示用户是否启用
    }

    // 扩展方法：获取邮箱
    public String getEmail() {
        return user.getEmail();
    }

    // 扩展方法：获取电话号码
    public String getPhone() {
        return user.getPhoneNumber();
    }
}
```







---


### 3、Spring Security 认证与授权流程

1. ==用户请求==：
	1. <font color="#00b0f0">用户请求</font>：
		1. 用户发起对受 Spring Security 保护资源的访问的每一个请求，都会触发认证和授权流程。
	2. <font color="#00b0f0">补充：跨域请求处理</font>：
		2. 在前后端分离的项目中，前端和后端可能运行在不同的域名或端口上。此时需要配置 `CorsFilter` 来处理跨域请求，并设置适当的 CORS 策略（如允许的来源、方法和头信息），以避免浏览器阻止跨域请求。
2. ==过滤器介入==：
	1. <font color="#00b0f0">过滤器介入</font>：
		1. `SecurityContextPersistenceFilter` 过滤器 和 `UsernamePasswordAuthenticationFilter` 过滤器依次介入
	2. <font color="#00b0f0">`SecurityContextPersistenceFilter` 过滤器介入</font>：
		2. 该过滤器用于检查服务器端的 `HttpSession`，查找是否存在 `SecurityContext`（作用是保管用户**详细信息**的身份证 `Authentication` 对象）
		3. 若存在 `SecurityContext`，便将其加载到本线程的 `SecurityContextHolder` 中
		4. 若不存在 `SecurityContext`，则自动初始化一个新的 `SecurityContext`，并加载到本线程的 `SecurityContextHolder` 中
		5. 在基于 JWT 的无状态认证机制中，我们会禁用该过滤器，以确保每个请求都能保持无状态特性。
	3. <font color="#00b0f0">`UsernamePasswordAuthenticationFilter` 过滤器介入</font>：
		1. 该过滤器会自动将请求中的**认证信息**（即前端发送的登录信息）封装为 `UsernamePasswordAuthenticationToken` 对象。该对象是 `Authentication` 接口的一个实现类，随后会被 `AuthenticationManager` 用于进行身份验证
		2. 这种方式主要适用于 Spring Security 默认的表单登录场景
		3. 如果使用自定义认证流程（例如基于 JWT 的无状态认证机制），则需要禁用该过滤器，并手动封装 `UsernamePasswordAuthenticationToken` 对象
3. ==加载用户信息==：
	1. <font color="#00b0f0">加载用户信息</font>：
		1. 创建封装用户**详细信息**的 `UserDetails` 实例，该实例将在 `AuthenticationManager` 中与传入的 `UsernamePasswordAuthenticationToken` 对象所携带的**认证信息**进行比对
		2. 是否需要手动加载，可参考下面的补充内容：用户信息加载方式
	2. <font color="#00b0f0">补充：用户信息加载方式</font>：
		1. <font color="#5f497a">内存存储</font>：
			1. 将用户信息直接存储在配置文件或配置类中，适合测试环境或单用户、少用户场景，不需要手动加载用户信息
		2. <font color="#5f497a">数据库存储</font>：
			2. 需要手动通过 `UserDetailsService` 类进行数据库查询，将查询结果注入到 `UserDetails` 后，手动将其加载到 `AuthenticationManagerBuilder` 中
			3. 可以根据业务需求自定义 `UserDetails` 实现类（如 `MyUserDetails`），以包含更多用户相关数据（如部门、权限范围等）
	3. <font color="#00b0f0">补充：密码加密</font>：
		1. 实际应用中，用户密码必须存储为加密形式（如 BCrypt），并在认证过程中进行加密验证。切勿明文存储或传输密码
4. ==实现认证==：
	1. 在 `AuthenticationManager`中传入封装用户**认证信息**的 `AUsernamePasswordAuthenticationToken` 对象，让其与封装用户真实信息的 `UserDetails` 对象进行对比，验证其有效性
	2. 若认证成功，`AuthenticationManager` 会返回一个包含用户**详细信息**的 `Authentication` 对象（`UserDetails` 被封装为其 `Principal` 属性），并自动将其存储在 `SecurityContextHolder` 中，此对象就是经过 Spring Security 认证后在本次请求中的凭证，可以被应用程序中的任何部分访问，以确定用户的身份和权限。
	3. 若认证失败（如用户名不存在或密码错误），需要捕获异常（如 `BadCredentialsException`）并返回适当的错误响应（如 HTTP 401 Unauthorized）
5. ==进行授权==：
	1. <font color="#00b0f0">进行授权</font>：Spring Security 会根据为用户设置的权限，判断该用户是否有权访问特定资源或方法。这个授权过程主要是通过 `FilterSecurityInterceptor` 过滤器来实现的。
	2. <font color="#00b0f0">`FilterSecurityInterceptor` 过滤器介入</font>：
		1. 过滤器 `FilterSecurityInterceptor` 会通过当前线程的 `SecurityContextHolder` 获取 `SecurityContext` 中的 `Authentication` 对象。通过该对象，过滤器判断用户的权限，然后根据权限决定是否允许访问对应的资源或方法。
6. ==请求结束==：
	1. <font color="#00b0f0">请求结束</font>：
		1. 在请求处理完成后，Spring Security 会根据配置决定是否将 `SecurityContext` 存储到 `HttpSession` 中，随后会清空 `SecurityContextHolder`。这一过程由 `SecurityContextPersistenceFilter` 过滤器介入执行。
	2. <font color="#00b0f0">`SecurityContextPersistenceFilter` 过滤器再次介入</font>：
		2. `SecurityContextPersistenceFilter` 会再次介入，负责从当前请求线程的 `SecurityContextHolder` 中提取 `SecurityContext`。如果 `SecurityContext` 存在且系统配置了将其保存至会话，该过滤器会将 `SecurityContext` 存回到 HTTP session 中。
		3. 接着，过滤器会清空 `SecurityContextHolder`，以确保 `SecurityContext` 不会在后续请求中被无意复用，保证每个请求都会触发认证和授权流程。

---


### 4、Spring Security 加密功能

#### 4.1、加密概述

在实际应用中，不能以明文形式存储用户密码，因为这样会带来严重的安全风险，为了确保安全性，密码必须经过加密处理后存储。

此外，在 API 中验证密码时，也应使用加密后的密码进行比对，避免明文存储或传输密码，以保障用户数据的安全性。

而 `PasswordEncoder` 是 Spring Security 提供的一个接口，主要用于对用户的密码进行**加密**和**验证**。
1. <font color="#00b0f0">加密密码：</font>将明文密码转化为密文密码（即不可逆的加密过程）。
2. <font color="#00b0f0">验证密码</font>：将用户输入的密码与存储的加密密码进行比较，验证用户输入的密码是否正确。

---


#### 4.2、PasswordEncoder

##### 4.2.1、PasswordEncoder 概述

`PasswordEncoder` 是 Spring Security 提供的一个接口，主要用于对用户的密码进行**加密**和**验证**。
1. <font color="#00b0f0">加密密码：</font>将明文密码转化为密文密码（即不可逆的加密过程）。
2. <font color="#00b0f0">验证密码</font>：将用户输入的密码与存储的加密密码进行比较，验证用户输入的密码是否正确。

---


##### 4.2.2、PaswordEncoder 常用实现

Spring Security 提供了几种常见的 `PasswordEncoder` 实现类，常用的有：
1. <font color="#00b0f0">BCryptPasswordEncoder</font>：基于 BCrypt 算法的密码加密工具，提供较高安全的密码加密方式，能够有效防止暴力破解，适用于大多数应用。
2. <font color="#00b0f0">NoOpPasswordEncoder</font>：不对密码进行加密，直接返回原始密码，通常用于开发或测试环境，不建议在生产环境中使用
3. <font color="#00b0f0">Pbkdf2PasswordEncoder</font>：基于 PBKDF2 算法的密码加密工具。
4. <font color="#00b0f0">SCryptPasswordEncoder</font>：基于 scrypt 算法的密码加密工具。

---


##### 4.2.3、PasswordEncoder 使用步骤

==1.配置 PasswordEncoder==
根据 Spring Security 的要求，无论是否对密码进行加密，都必须配置一个密码加密器。具体来说，需要在 Spring Security 配置类中选择适当的实现类，并将其声明为一个 Bean。
``` java
// SecurityConfig.java

@Configuration
@EnableWebSecurity 
public class SecurityConfig {
    @Bean 
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(); // 选择实现类
    }
}
```


==2.加密密码==
```java
/**
* PasswordEncoder 的加密方法：
* 1. passwordEncoder.encode(rawPassword)
*/


@Controller  
@RequestMapping("/security")  
public class PasswordController {  
    private final PasswordEncoder passwordEncoder;  
  
    @Autowired  
    public PasswordController(PasswordEncoder passwordEncoder) {  
        this.passwordEncoder = passwordEncoder;  
    }  
      
    // 通过方法处理密码加密  
    @RequestMapping("/encode-password")  
    @ResponseBody  
    public String encodePassword() {  
        String password = "myPasswordxxxxxxx";  
        // 在方法内调用 passwordEncoder 进行密码加密  
        String encodedPassword = passwordEncoder.encode(password);  
        return "Encoded Password: " + encodedPassword;  
    }  
}
```


==3.验证密码==
```java
/**
* PasswordEncoder 的验证方法：
* 1. passwordEncoder.matches(rawPassword,encodedPassword)
*/
@Controller
@RequestMapping("/security")  
public class PasswordController {

    private final PasswordEncoder passwordEncoder;

    @Autowired
    public PasswordController(PasswordEncoder passwordEncoder) {
        this.passwordEncoder = passwordEncoder;
    }

    // 通过方法验证密码
    @RequestMapping("/verify-password")
    @ResponseBody
    public String verifyPassword() {
        String rawPassword = "myPasswordxxxxxxx";  // 用户输入的原始密码，不需要再对原始密码进行加密
        String encodedPassword = passwordEncoder.encode(rawPassword);  // 模拟数据库中的加密密码

        // 使用 matches 方法验证密码是否匹配
        boolean matches = passwordEncoder.matches(rawPassword, encodedPassword);
        
        return matches ? "Password is valid" : "Invalid password";
    }
}
```

---


### 5、Spring Security 安全防御功能

#### 5.1、防御概述

Spring Security 是一个强大且灵活的安全框架，针对常见的安全威胁提供了全面的防护措施，有效提升应用程序的安全性。

---


#### 5.2、实现防御

可以通过配置类中的 `SecurityFilterChain` Bean 来实现部分 http 的安全配置。常见的配置选项包括：

```java
/**
 * 常见 http 安全配置选项：
 * 
 * 1. http.http.authorizeHttpRequests()：资源访问控制，默认情况下，所有经过认证的用户都可以访问所有资源，且所有路径都需要经过认证
 * 
 * 2. http.formLogin()：启用表单登录，默认启用，使用 Spring Security 内置的登录页面，路径为 /login
 * 
 * 3. http.logout()：启用注销功能，默认启用，用于清理会话和 Remembe r-Me，路径为 /logout
 * 
 * 4. http.csrf()：启用跨站点请求伪造（CSRF）防护，默认启用
 * 
 * 5. http.cors()：启用和配置跨域资源共享（CORS），默认不启用
 * 
 * 6. http.addFilterBefore()：配置自定义过滤器，将其添加到指定的过滤器链位置
 * 
 * 7. http.sessionManagement()：配置会话管理策略。默认情况下，在使用内置登录方式登录后，SecurityContext 会被存储到 HttpSession 中
 */
 
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    @Autowired
    private CustomUserDetailsService customUserDetailsService;
    
    @Autowired
	private JwtRequestFilter jwtRequestFilter;

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    
        // 1. 请求授权控制
        http.authorizeHttpRequests(auth -> auth
            .requestMatchers("/public/**", "/error").permitAll() // 路径 /public/** 和 /error，任何用户都可访问
            .requestMatchers("/admin/**").hasRole("ADMIN") // 路径 /admin/**，只有 ADMIN 角色的用户可以访问
            .anyRequest().authenticated() // 所有路径都要经过认证
            // 如果要放行所有请求，可以配置 http.authorizeHttpRequests(auth -> auth.anyRequest().permitAll());
        );
        
		// 2. 启用表单登录
        http.formLogin(); // 如果要关闭表单登录，可以配置 http.formLogin(form  -> form.disable()); 
        
		// 3. 启用注销功能
        http.logout(); // 如果要关闭注销功能，可以配置 http.logout(logout  -> logout.disable());
        
		// 4. 启用跨站点请求伪造（CSRF）防护
        http.csrf(); // 如果要关闭 CSRF 防护，可以配置 http.csrf(csrf  -> csrf.disable());

        // 5. 启用和配置跨域资源共享（CORS）
        http.cors(cors -> cors.configurationSource(request -> {
            CorsConfiguration config = new CorsConfiguration();
            config.setAllowedOrigins(List.of("https://example.com")); // 允许的跨域源
            config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE")); // 允许的 HTTP 方法
            config.setAllowedHeaders(List.of("Authorization", "Content-Type")); // 允许的请求头
            return config;
        }));

		// 6. 配置自定义过滤器
        http.addFilterBefore(jwtRequestFilter, UsernamePasswordAuthenticationFilter.class); // 在用户名密码过滤器之前添加 JWT 过滤器
        
        // 7. 会话管理策略
		http.sessionManagement(session -> session  
        .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)  
        // 如果要禁用会话 Session，可以直接关闭相关过滤器，或者配置http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS); 
		)；
        return http.build(); // 构建 SecurityFilterChain 对象
    }
}
```

----


### 6、Spring Security 认证功能

#### 6.1、认证概述

==1.什么是认证==

<font color="#ff0000">认证就是验证用户身份的过程，即用户登录过程。</font>在常规的 Java Web 开发中，通常会手动编写过滤器来过滤每一个 API 请求，验证请求中是否携带有效且安全的凭证。
	1. 如果凭证有效，用户可以继续进行 API 操作。
	2. 如果凭证不存在、无效或不安全，我们会将此视为用户首次登录，将用户重定向到登录页面，要求重新登录。

在登录页面，用户输入用户名和密码等信息并提交给登录 API。系统根据用户名查找数据库中的用户记录，查询到密码并验证密码是否匹配。
1. 如果密码正确，认证通过，用户登录成功。成功登录后，系统返回一个凭证（例如 `JSESSIONID` 或 `JWT`，即 Web 的“记住我”功能）。

在接下来的请求中，过滤器会继续检查每个 API 请求。如果请求中包含有效且安全的凭证，过滤器通常会解析出一个认证标识符（例如“OK”），表明用户已经通过认证。此时，用户无需重新登录，可以继续进行 API 调用。



==2.Spring Security 是怎么实现认证的？==

在 Spring Security 中，认证的判断依据是当前线程是否包含 `Authentication` 对象，这个对象包含了用户的详细信息。

同样的，在 Spring Security 中，过滤器会过滤每一个受保护的 API 请求，验证请求中是否携带有效且安全的凭证。
	1. 如果凭证有效，用户可以继续进行 API 操作。
	2. 如果凭证不存在、无效或不安全，我们会将此视为用户首次登录，将用户重定向到登录页面，要求重新登录。

在登录页面，用户输入用户名和密码等信息并提交给登录 API。系统根据用户名查找数据库中的用户记录（即加载用户信息），通常是通过 `UserDetailsService` 来获取 `UserDetails`（UserDetails 封装了用户的基本信息，如用户名、密码、权限、账户有效性等。），接着，系统会将 `UserDetails` 中的信息与用户提供的认证信息进行匹配
1. 如果成功匹配，`UserDetails` 将被封装成 `Authentication` 对象，并保存在当前线程中
2. 
3. 
4. 如果密码正确，认证通过，用户登录成功。成功登录后，系统返回一个凭证（例如 `JSESSIONID` 或 `JWT`，即 Web 的“记住我”功能）。





用户首次请求时，过滤器会拦截请求并将其重定向到登录页面。用户提交用户名和密码，系统通过用户名加载用户信息，通常是通过 `UserDetailsService` 来获取 `UserDetails`，它封装了用户的基本信息，如用户名、密码、权限等。系统会将 `UserDetails` 中的信息与用户提供的认证信息进行匹配，。

接下来，系统会根据“记住我”功能返回一个凭证（例如 `JSESSIONID` 或 `JWT`）。该凭证会被解析，并通过解析出的 `Authentication` 信息，用户便可以在不重新登录的情况下直接进行 API 调用。


<font color="#ff0000">在 Spring Security 中，是否经过认证的判断，依赖于当前线程中是否存在包含用户详细信息的 `Authentication` 对象。</font>

当用户首次请求时，过滤器会<font color="#6425d0">拦截请求</font>并将其重定向到登录页面。用户需要提供用户名和密码等，发送至登录 API。系统会在根据用户名，进行<font color="#6425d0">加载用户信息</font>，即调用 `UserDetailsService` 生成 `UserDetails`，内部封装了用户的信息，例如用户名、密码、权限、身份有效性等。然后会将 `UserDetails` 中封装的信息，与用户的认证信息进行匹配，如果匹配成功，我们会将 `UserDetails` 封装为 `Authentication` ，并将其保存到本线程，然后会根据 Web 记住我方式，选择返回一个凭证，通过该凭证，我们能解析出 `Authentication`，然后就可以直接进行 API 调用。



---


#### 6.2、实现认证
1. ==选择用户信息存储的方式==
	1. 根据生产或开发环境的需求，灵活选择用户信息的存储方式
	2. [[笔记：用户信息存储的方式]]
2. ==选择加载用户信息的方式==
	1. 根据用户信息的存储方式，选择对应的加载方式
	2. [[笔记：加载用户信息的方式==]]
3. ==选择认证的方式==
	1. 根据业务需求，选择合适的认证方式
	2. [[笔记：认证的方式]]
4. ==选择记住我的方式==
	1. 根据业务需求，选择是否启用记住我功能，并选择实现方式
	2. [[笔记：Spring Security#8、Spring Security 记住我功能|记住我的方式]]
5. ==选择登录的方式==
	1. 根据记住我的方式，选择登录的方式
	2. [[笔记：登录的方式]]


---


### 7、Spring Security 授权功能

#### 7.1、授权概述

授权是指通过为用户分配角色或权限，控制对资源和方法的访问权限，实际上就是对资源和方法进行“上锁”。在 Spring Security 中，授权功能包括**角色分配**和**访问控制**。

---


#### 7.2、实现角色分配

##### 7.2.1、角色分配概述

实现角色分配，我们我们可以为用户分配角色（roles）或权限（authorities）：
1. **角色（roles）**：通常是一个用户的身份标识（例如 `USER`），通常用于控制资源访问权限
2. **权限（authorities）**：通常是用户可以执行的具体操作(例如 `USER_DELETE`)，权限则可以精细化到具体操作

由于用户信息通常存储在内存或数据库中，下面将分别介绍这两种情况的角色分配实现方法：
1. ==内存角色分配==
2. ==数据库角色分配==

> [!NOTE] 注意事项
> 1. 如果同时设置角色和权限，后设置的将覆盖先设置的，我们可以通过 `Authentication` 对象获取用户的角色分配
> 2. `USER` 并不拥有 `User_Delete` 权限，一个萝卜一个坑

---


##### 7.2.2、内存角色分配

内存中的用户信息可以存储在配置文件或配置类中。接下来，针对这两种情况，分别介绍如何进行内存用户分配：

==1.配置文件==
```yaml
spring:  
  security:  
    user:  
      name: user  
      password: password  
      roles: USER
```


==2.配置类==
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public AuthenticationManager authenticationManager(HttpSecurity http) throws Exception {
        // 1. 获取 authenticationManagerBuilder 对象
        AuthenticationManagerBuilder authenticationManagerBuilder =  
                http.getSharedObject(AuthenticationManagerBuilder.class);  
  
        // 2. 配置内存中的用户  
        authenticationManagerBuilder.inMemoryAuthentication()  
                .withUser("user") // 用户名  
                .password(passwordEncoder().encode("password"))  // 密码  
                .roles("USER") // 用户权限  
                .accountExpired(false) //  账户是否过期，false 表示没有过期，true 表示过期，用户无法登录
                .accountLocked(false)  // 账户是否锁定，false 表示未锁定，true 表示锁定，用户无法登录
                .credentialsExpired(false)  
                .disabled(false)  
                .and()  
                .withUser("admin")  
                .password(passwordEncoder().encode("admin"))  
                .roles("ADMIN")  
                .accountExpired(false)  
                .accountLocked(false)  
                .credentialsExpired(false)  
                .disabled(false);  
                
		// 3. 构建 AuthenticationManager 并返回
        return authenticationManagerBuilder.build();  // 这里使用 build() 方法来构建 AuthenticationManager    }  
}

```

---


#### 7.2.3、数据库角色分配

| 列名   | 数据类型                  | 说明                  |
| ---- | --------------------- | ------------------- |
| id   | BIGINT AUTO_INCREMENT | 角色 ID，主键，自动递增       |
| role | VARCHAR(50) NOT NULL  | 权限名称，唯一，如：ROLE_USER |

---


#### 7.3、实现访问控制

##### 7.3.1、资源访问控制

在配置 Spring Security 的[[笔记：Spring Security#7.2、Spring Security 实现防御|安全防御功能]]时，可以通过 `http.authorizeHttpRequests` 设置资源的访问控制，通常分为基于 roles 和基于 authorities 的控制方式：

> [!NOTE] 注意事项
> 1. 资源访问控制，默认情况下，所有经过认证的用户都可以访问所有资源，且所有路径都需要经过认证

==1.基于 roles==
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
	            // 只有 ADMIN 角色可以访问 /admin/**
                .requestMatchers("/admin/**").hasRole("ADMIN")
                // USER 或 ADMIN 角色可以访问 /user/**
                .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN")
                // 任何请求都需要认证(包括登录和注销)，如果要放行所有请求，可以配置 .anyRequest().permitAll()
                .anyRequest().authenticated() 
            )

        return http.build();
    }
}
```


==2.基于 authorities==
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                // 只有具有 ADMIN 权限的用户可以访问 /admin/**
                .requestMatchers("/admin/**").hasAuthority("ADMIN")
                // 具有 USER 或 ADMIN 权限的用户可以访问 /user/**
                .requestMatchers("/user/**").hasAnyAuthority("USER", "ADMIN")
                // 任何请求均需要认证
                .anyRequest().authenticated()
            )
            .formLogin(withDefaults()); // 登录放行，启用表单登录
        return http.build();
    }
}
```

---


##### 7.3.2、方法访问控制

Spring Security 允许在使用注解进行方法级别的授权，常用的注解有 `@PreAuthorize`，一般在**服务层（Service 层）** 进行控制。

> [!NOTE] 注意事项
> 1. 一般基于权限（authorities）对资源进行授权。
> 2. 若要使方法授权生效，需要在配置类上添加注解：`@EnableMethodSecurity`

==1.配置类上添加注解==
```java
@Configuration  
@EnableWebSecurity  
@EnableMethodSecurity  
public class SecurityConfig {  
  ......
}
```


==2.方法访问控制==
```java
@Service
public class UserService {
    @PreAuthorize("hasRole('ADMIN')")
    public void adminMethod() {
        // 只有 ADMIN 角色可以执行该方法
    }

    @PreAuthorize("hasAuthority('USER_READ')") 
    public void readUser() {
        // 只有具有 USER_READ 权限的用户可以执行
    }
}
```

---


### 8、Spring Security 记住我功能


# 二、实操

### 1、环境准备



### 2、创建 Spring Boot 项目
->->->[[笔记：创建 Java 项目#4、创建 Spring Boot 项目|创建 Spring Boot 项目]]

---


### 3、引入 Spring Security 起步依赖

引入 [Spring Security 起步依赖](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-security)
```xml
<dependency> 
	<groupId>org.springframework.boot</groupId> 
	<artifactId>spring-boot-starter-security</artifactId> 
	<version>xxxxxx</version>
</dependency>
```

---


### 4、创建 Spring Security 配置类

``` java
// SecurityConfig.java

@Configuration
public class SecurityConfig {
	......
}
```

> [!NOTE] 注意事项
> Spring Security 的配置主要在 Java 配置类中进行，而不是在 `application.yml` 文件中，因为安全配置通常涉及到逻辑和条件判断，这些无法简单地通过属性文件表达。

----


### 5、启用 Spring Security 功能

在 Spring Security 配置类中使用 `@EnableWebSecurity` 注解，以启用 Spring Security 功能。
``` java
// SecurityConfig.java

@Configuration
@EnabledWebSecurity // 启用 Spring Security 功能
public class SecurityConfig {
	......
}
```

---


### 6、配置密码加密器
->->->[[笔记：Spring Security#4.2.3、PasswordEncoder 使用步骤|PasswordEncoder 使用步骤]]

---


### 7、配置安全防御功能
->->->[[笔记：Spring Security#7、Spring Security 安全防御功能|配置安全防御功能]]

---


### 8、实现角色分配
->->->[[笔记：Spring Security#6.2、实现角色分配|实现角色分配]]

---


### 9、实现访问控制
->->->[[笔记：Spring Security#6.3、实现访问控制|实现访问控制]]

---


### 10、实现认证
->->->[[笔记：Spring Security#6.2、实现认证|实现认证]]

---

