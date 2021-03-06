2020.01.29

## Spring Security
 - 스프링 기반의 어플리케이션 보안(인증과 권한)을 담당하는 프레임워크
   + Spring Security 를 사용하지 않는다면 자체적으로 세션을 체크하고, redirect 등을 해야할 것
   + Spring Security 는 보안과 관련해서 체계적으로 많은 옵션들로 이를 지원
 - Filter 기반으로 동작하기 때문에 Spring MVC 와 분리되어 관리 및 동작
 
## Spring Security 의 구조
 - Http Request → AuthenticationFilter → AuthenticationManager → AuthenticationProvider   
   → UserDetailsService → UserDetails(Interface) → UserDetailsService   
   → Database   
   → UserDetailsService → AuthenticationProvider → AuthenticationManger → AuthenticationFilter   
   → SecurityContextHolder< SecurityContext < Authencation > >
 - Spring Security 는 Session-Cookie 방식으로 인증
   1. 유저가 로그인을 시도 (HTTP-Request)
   2. AuthenticationFilter 에서부터 위와 같이 DB에 접속
   3. Database 에 저장된 데이터라면 User Details 로 꺼내서 사용자의 Session 생성
   4. Spring Security 의 인메모리 세션저장소인 SecurityContextHolder 에 저장
   5. 유저에게 Session ID 와 함께 응답을 내려줌
   6. 이후 요청에서는 요청 쿠키에서 Session ID를 검증 후 유효하면 Authentication 을 쥐어준다
   
## Security 의 Filter
 - SecurityContextPersistenceFilter : SecurityContextRepository 에서 SecurityContext 를 가져오거나   
   저장하는 역할을 수행
 - LogoutFilter : 설정된 로그아웃 URL 로 오는 요청을 감시하며, 해당 유저를 로그아웃 처리
 - AuthenticationFilter : (ID 와 PW 를사용하는 Form 기반 인증) 설정된 로그인 URL 로 오는 요청을 감시하며,   
   유저 인증 처리   
   1. AuthenticationManager 를 통한 인증 실행
   2. 인증 성공 시, Authentication 객체를 SecurityContext 에 저장 후, AuthenticationSuccessHandler 실행
   3. 인증 실패 시, AuthenticationFailureHandler 실행
 - DefaultLoginPageGeneratingFilter : 인증을 위한 로그인 폼 URL 을 감시
 - BasicAuthenticationFilter : HTTP 기본 인증 헤더를 감시하여 처리
 - SessionManagementFilter : 이 필터를 인증된 사용자와 관련된 모든 세션을 추적
 
   
public interface Authentication extends Principal, Serializable {   
   
   //Authentication 저장소에 의해 인증된 사용자의 권한 목록   
   Collection<? extends GrantedAuthority> getAuthorities();   
      
   Object getCredentials();   //주로 비밀번호   
   Object getDetails();	      //사용자 상세정보   
   Object getPrincipal();     //주로 아이디   
   boolean isAuthenticated(); //인증 여부   
   void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException;   
}   

## 설정방법
 1. 의존성 추가
 2. web.xml 에 SpringSecurityFilterChain 등록, context 등록
 3. security 설정 (security-context.xml)
   
