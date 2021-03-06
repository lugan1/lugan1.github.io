---
title: "[spring] Intercepter "
tags:
- log
- spring
categories:
- spring
date: '2021-10-20 09:46:00'
classes: wide
---

# Spring Intercepter
- 로그인이 안되어 있을시 리다이렉트 구현시에, 많은 API 들에 로그인이 되어있는지 확인 하는 코드를 중복해서 똑같이 집어넣으면, 가독성이 떨어지고, 중복성이 심해진다.

- 이럴때 HandlerIntercepter 인터페이스를 사용한다.

<br/>
<br/>

# Spring MVC Lifecycle
![spring_mvc_life_cycle.png](/assets\image\posts_image/spring_mvc_life_cycle.png)

<br/>
<br/>

## Intercepter 까지 가는 과정
Dispatch Servlet => Handler Adpater => Interceptor

<br/>
<br/>


# Handler Intercepter
- 다수의 컨트롤러에 대해 동일한 기능을 똑같이 적용해야 할 때 사용한다.

- **org.springframework.web.HandlerInterceptor** 인터페이스를 사용한다.

- 다음과 같은 시점에서 인터셉터를 발동할 수 있다.
   - 컨트롤러(핸들러) 실행전
   - 컨트롤러(핸들러) 실행 후, 아직 뷰를 실행하기 전
   - 뷰를 실행한 이후


<br/>
<br/>
<br/>

# Handler Intercepter는 다음과 같은 메서드를 정의하고 있다.

## 1. preHandle()
- 컨트롤러(핸들러) 객체를 실행하기 전에 필요한 기능을 구현할 때 사용한다.
- 리턴 타입은 boolean 이다. preHandle() 이 false 를 리턴하면 컨트롤러(또는 다음 HandlerInterceptor)를 실행하지 않는다.

- Params ( Object handler ) 
  - 웹 요청을 처리할 컨트롤러 객체
  - 로그인하지 않은 경우 컨트롤러를 실행 안시키게 할수 있음
  - 컨트롤러를 실행하기 전에 컨트롤러에서 필요로 하는 정보를 생성

```java
boolean preHandle(HttpServletRequest request, HttpservletResponse response, Object handler) throws Exception;
```


<br/>
<br/>

## 2. postHandle()
- 컨트롤러(핸들러)가 정상적으로 실행된 이후에 추가기능을 구현할 때 사용한다.
- 컨트롤러가 Exception 을 발생하면 PostHandle() 메서드는 실행하지 않는다.

```java
void postHandle(HttpServletRequest request, HttpservletResponse response, Object handler, ModelAndView modelAndView) throws Exception;
```

<br/>
<br/>

## 3. afterCompletion()
- 컨트롤러 실행 이후에 예기치 않게 발생한 익셉션을 로그로 남긴다거나, 실행 시간을 기록하는 등의 후처리를 하기에 적합한 메소드다.

- 뷰가 클라이언트에 응답을 전송한 뒤에 실행된다.

- 컨트롤러 실행 과정에서 익셉션이 발생하면 이 메서드의 네 번째 파라미터로 전달된다.
  - Exception ex

- 익셉션이 발생하지 않으면 네 번째 파라미터는 null 이 된다.


```java
void afterCompletion(HttpServletRequest request, HttpservletResponse response, Object handler, Exception ex) throws Exception;
```



# HandlerInterceptor와 컨트롤러의 실행 흐름 (Process)

\<그림\>


<br/>
<br/>

### 로그인 여부에따라 페이지를 못들어오게 리다이렉트 하는 것은 **HandlerInterCepter 인터페이스에서 preHandler() 메소드를 사용한다.**

<br/>
<br/>

## 인터셉터의 preHandler 사용, 로그인 안되어있을시 리다이렉트 예제
```java
public class StudyAuthCheckIntercepter implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession httpSession = request.getSession(false);
        if (httpSession != null){
            // httpSession이 만들어져 있는지 확인

            Object authInfo = httpSession.getAttribute("authInfo");
            // httpSession 에서 authInfo가 들어가 있는지 확인
            if (authInfo != null){
                return true;
                // true 리턴하면 컨트롤러의 API 실행
            }
        }
        response.sendRedirect(request.getContextPath());
        return false;
        // false 리턴하면 컨트롤러의 API 실행 안함
    }
}
```

<br/>

### 인터셉터를 만들었으면, 그 인터셉터를 어디에 적용해야 할지 설정해야 한다. **WebMvcConfigurer 를 implements 한 MvcConfig 클래스에서 인터셉터를 설정한다.**
<br/>

**인터셉터 설정 예제)**
```java
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
	
	@Bean
	public StudyAuthCheckIntercepter studyAuthCheckIntercepter() {
		return new StudyAuthCheckIntercepter();
	}

	@Override
	public void addInterceptors(InterceptorRegistry registry) {

		registry.addInterceptor(studyAuthCheckIntercepter()).addPathPatterns("/api/back/board/**");
		// addPathPatterns : 인터셉터를 적용할 경로 패턴을 지정한다.
		// Ant 경로 패턴을 사용한다.

    }
}
```

### Ant 경로 패턴
- *, **, ? 의 세가지 특수 문자를 이용해서 경로를 표현한다.

<br/>

**\*** \: 0개 또는 그 이상의 글자  
**?** : 1개 글자  
**\*\*** : 0개 또는 그 이상의 폴더 경로

<br/>

### Ant 경로 패턴 사용 예제
```java
@RequestMapping("/member/?*.info")
// /member/로 시작하고 확장자가 .info로 끝나는 모든 경로

@RequestMapping("/fqq/f?OO.fq")
// /fqq/f 로 시작하고, 1글자가 사이에 위치하고 OO.fq 로 끝나는 모든 경로

@RequestMapping("/folders/**/files")
// /folders/ 로 시작하고, 중간에 0개 이상의 중간 경로가 존재해ㅏ고 /files로 끝나는 모든 경로. 예를 들어 /folder/files, /folders/1/2/3/files 등등..
```

<br/>
<br/>

## exlucdePathPatterns()
- addPathPatterns() 메서드에 지정한 경로 패턴 중 일부를 제외하고 싶을 때 사용한다.


<br/>
<br/>
<br/>
<br/>



# Spring Security 사용 Inteceptor 만들기
### 참고사이트 :  
[https://soon-devblog.tistory.com/5?category=1026232](https://soon-devblog.tistory.com/5?category=1026232)

- Spring-scurity를 사용하면 모든 요청은 Session을 발급받는다.
  - Session을 발급받으면 클라이언트의 쿠키에 JESSIONID라는 키로 SessionID가 저장된다.
  
  - AuthenticationFilter는 해당 요청의 JESSIONID(SessionID)를 확인하여 JESSIONID와 매핑되는 인증정보가 Security Context에 있는지 판단한다.

  - JESSIONID(SessionID)에 매핑되는 인증정보가 Security Context 내에 없으면 HTTP Error Code 를 발생시키거나, 리다이렉트 처리를 할 수 있다.

<br/>
<br/>

- AuthenticationFilter 는 기본적으로 로그인폼으로 오는 데이터를 username과 password로 인식하고 있다.
  - **Spring Security는 자동으로 HTML Login Form을 생성한다.**

![spring-security-form.png](/assets\image\posts_image\spring-security-form.png)

  - 따라서 **처리하려는 로그인 데이터 양식도 생성된 Login Form 에 맞춰서 username / password 로 변수명을 통일시켜줘야 한다.**
  - Spring-security는 dafault 설정으로 Mapping URL을 /login 으로 설정했는데, **프론트에서 오는 로그인 요청도 /login 으로 통일시켜 줘야한다.**

<br/>
<br/>

- AuthenticationFilter는 입력받은 username / password를 이용해 UsernamePasswordAuthenticationToken 을 만든다.
  - 통칭 AuthenticationToken 은 Authentication 인터페이스의 구현체다.
  - AuthenticationToken에 있는 username / password가 유효한 계정인지 판단하기 위해 AuthenticationMangaer에게 위임한다.

<br/>
<br/>

- AuthenticationManager는 등록한 AuthenticationProvider들을 연쇄적으로 실행시킨다. 
  - AuthenticationProvider의 구현체에서는 다음과 같은 작업이 필요하다.
  - (1). AuthenticationToken에 있는 계정 정보가 유효한지 판단하는 로직 (DB로부터 조회)
  - (2). 계정 정보가 유효하다면 유저의 상세정보(이름, 나이 등 필요한 정보)를 이용해 새로운 UserPasswordAuthenticationToekn 을 발급

<br/>
<br/>

- 새롭게 발급받은 AuthenticationToken은 Security Context에 저장된다.

<br/>
<br/>

- 계정 정보가 유효하다면 AuthenticationFilter는 AuthencationSuccessHandler에 따라 요청을 redirect 시킨다.


<br/>
<br/>

### 요약
Session을 발급받는다 -> <br/>
클라이언트 쿠키에 JSSESSIONID 가 생성된다. -> <br/>
filter는 JSSESSIONID 가 Security context에 있는지 확인한다 -> 
있으면 Token을 발급시킨다 -> <br/>
Manager는 등록한 Provider들을 연쇄 실행시킨다 -> <br/> 
Provider는 Token에 있는 계정정보가 유효한지 DB로부터 조회한다 -> <br/>
정보가 유효하면 Provider는 Token 을 발급시킨다. -> <br/>
발급된 Token은 Security Context에 저장된다. -> <br/>
계정 정보가 유효하면 Filter는 SusccesHandler에 따라 요청을 redirect 시킨다.

<br/>
<br/>
<br/>

## Spring Security 구현하기
- Maven dependency에 스프링 시큐리티를 추가한다.

<br/>

- WeboSecurityConfig 클래스를 생성한다.
  - WebSecurityConfigurerAdapter를 상속받는다.

<br/>

- 다음과 같은 설정들을 추가한다.
  - 어떤 요청에 대해서 인증을 요구할 것인지
  - 특정 요청에 대해서 어떤 권한을 요구할 것인지
  - 인증되지 않은 요청을 어떤 url로 redirect시킬지
  - 로그인 성공 후 어느 화면으로 이동시킬지
  - 로그아웃시 어떤 작업을 수행시킬지 
  - 403에러에 대해 어떻게 처리할지
  - Authentication 유효성을 어떻게 판단할지 

<br/>

- configure 를 Override 해서 구현한다.

<br/>

- AuthenticationSuccessHandler class 를 생성한다.
  - SavedRequestAwareAuthenticationSuccessHandler 를 상속받는다.
  - 인증 성공 후에 Redirect시킬 URL을 설정한다.

<br/>

- AuthenticationProvider를 구현한다.
  - Authentication 을 Override 한다.
  - 인증을 처리한다.

- AccessDeniedHandler 를 구현한다.
  - 인증에 성공했으나, 권한이 적합하지 않을경우 403 에러를 발생시킨다.


<br/>
<br/>
<br/>

**SecurityConfiguraiton 예제)**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Autowired
    private AuthProvider authProvider;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf().disable()
                .authorizeRequests()
                // authorizeRequest() 요청에 대한 인증/권한 설정을 담당한다.
                    .antMatchers("/","main").permitAll()
                    // ("/", "main") 요청은 인증 체크를 하지 않는다.
                    .antMatchers("/admin/*").hasAnyAuthority("ADMIN")
                    // ("admin/*")에 해당하는 요청은 "ADMIN" 권한이 있어야 접근 가능하다.

                    .anyRequest().authenticated()
                    // 위에서 정의한 요청을 제외한 모든 요청에 대해서는 인증을 요구한다.

                    .and()
                .formLogin()
                // 로그인 관련 설정을 담당한다.

                    .loginPage("/login")
                    // 아직 인증 전 상태이며 인증을 요구하는 요청이라면 "/login" 으로 redirect 시킨다.

                    .usernameParameter("userId")
                    // Spring Security에서 설정한 dafault 변수명인 username 을 userID 이름으로 변경시킨다.

                    .successHandler(new LoginSuccesHandler("/home"))
                    // 인증을 성공했을때 어떤 URL로 Redirect 시킬지 정의한다. 이를 위해 AuthenticationSuccessHandler 클래스를 생성해야 된다.

                    .permitAll()
                    .and()
                .logout()
                // 로그아웃 관련 설정을 담당한다.
                    .invalidateHttpSession(true)
                    // 로그아웃 요청이 오면 Session을 무효화 한다.
                    .deleteCookies("JESSIONID")
                    // 로그아웃 요청이오면 키-JESSIONID로 저장된 쿠키를 제거한다.
                    .permitAll()
                    .and()
                .authenticationProvider(authProvider)
                // 로그인 폼으로부터 오는 데이터가 유효한 계정 정보인지를 판단하기 위해 AuthenticationProvider를 구현했다.

                .exceptionHandling().accessDeniedHandler(accessDeniedHandler());
                // 인증에는 성공했으나 권한이 맞지 않을 경우 실행될 Handler를 등록한다.
    }
```
