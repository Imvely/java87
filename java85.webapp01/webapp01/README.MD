# step01 
- 프로젝트 폴더 준비

# step02
- 게시물 목록 조회 추가 = Spring + Mybatis + Servlet
 
# step03
- 게시물 상세조회 : BoardDetailServlet
- 게시물 목록에 "게시물 상세조회" 링크 추가

# step04
- 게시물 삭제 : BoardDeleteServlet
- 게시물 상세조회 페이지에 "게시물 삭제" 링크 추가
- 게시물 삭제 서블릿에 리프래시(refresh) 추가 

# step05
- 게시물 입력 폼 추가  : /board/form.html
- 게시물 목록조회 페이지에 "게시물 입력폼" 링크 추가
- 게시물 입력 : BoardAddServlet

# step06
- 게시물 변경 : BoardUpdateServlet
- 게시물 상세보기 페이지를 변경 가능 페이지로 바꾼다: BoardDetailServlet

# step07 (스프링 IoC 컨테이너 응용)
- 각 서블릿마다 개별적으로 만든 ApplicationContext(스프링 IoC 컨테이너)를 공유하기
- ApplicationContextHelper 객체 생성 : ApplicationContext를 리턴해주는 역할.
- 서블릿은 ApplicationContextHelper를 이용하여 IoC 컨테이너를 준비한다.
  BoardXxxServlet 클래스 변경하라!

# step08 (서블릿 응용)
- 서블릿들이 사용할 공통 자원을 준비시키는 AppInitServlet 생성
- web.xml에 AppInitServlet 정보를 등록한다.
- 모든 서블릿에 대해 SerlvetContext에 보관된 Spring IoC 컨테이너를 사용하도록 변경한다. 
- ApplicationContextHelper 클래스는 더이상 필요없다. 제거한다. 

# step09 (필터 응용)
- 필터를 이용하여 요청 파라미터의 문자집합을 지정한다. : CharacterEncodingFilter
- 모든 서블릿에서 request.setCharacterEncoding() 호출을 제거하라!
- 서블릿에서 스프링 IoC 컨테이너를 준비하는 대신
  필터에서 준비해보자! : AppInitFilter
- 기존의 AppInitServlet 클래스는 제거한다.  
  
# step10 (리스너 응용)
- 리스너를 이용하여 웹 애플리케이션이 시작될 때 스프링 IoC 컨테이너를 준비하자!
  ContextLoaderListener
- 웹 애플리케이션 DD 파일에 리스너를 등록하라!
  그리고 스프링 설정 파일의 위치 정보를 컨텍스트 파라미터에 설정하라!
- 기존의 AppInitFilter 클래스는 제거한다.

# step11 (Spring MVC 라이브러리의 필터와 리스너 사용)
- Spring MVC 프레임워크의 일부 기능 사용
  => 클라이언트가 POST 요청으로 보내는 데이터의 문자집합을 지정하는 필터
       org.springframework.web.filter.CharacterEncodingFilter
  => 스프링 IoC 컨테이너를 준비하는 리스너
       org.springframework.web.context.ContextLoaderListener
- 기존의 필터와 리스너는 제거한다.
- 스프링에서 준비한 IoC 컨테이너를 사용하려면 도우미 클래스를 이용해야 한다.
  예전처럼 ServletContext 객체에서 스프링 IoC 컨테이너를 꺼낼 수 없다.
  => 각 서블릿의 코드를 변경하라!

# step12 (프론터 컨트롤러 도입)
- 클라이언트 요청을 처리하는 서블릿의 관리자를 
  서블릿 컨테이너에서 스프링 IoC 컨테이너로 바꾼다.
  => 이점
     1) 스프링 IoC 컨테이너에서 일괄적으로 객체를 관리할 수 있다.
     2) 클라이언트 요청을 처리하는 것은 POJO 가 처리할 수 있다.
        (POJO: Plain Old Java Object. JavaSE 기술로 만든 객체)
  => 기존의 서블릿이 스프링 IoC 컨테이너의 관리 영역으로 넘어가기 때문에
     클라이언트의 요청을 받아서 넘겨주는 역할을 할 서블릿 클래스가 필요하다.
  => "프론트 컨트롤러(클라이언트 요청을 받아서 넘기는 역할을 할 서블릿)"과
     "워커(실제 클라이언트 요청을 처리하는 객체)"
      사이에 호출 규칙도 필요하다.
     
- 절차
1) 프론트 컨트롤러와 워커 사이에 호출 규칙을 정한다.
   => Worker 인터페이스 작성 
2) 기존 서블릿을 스프링이 관리하는 영역으로 옮긴다.
   => 서블릿 클래스를 Worker 구현체로 변경한다.
3) 클라이언트 요청을 전달할 서블릿 객체를 만든다.
   => DispatcherServlet 클래스 작성

# step13 (HttpServlet 클래스와 Redirect, Refresh의 응용)
- DispatcherServlet 클래스의 상위 클래스를 GenericServlet 클래스에서
  HttpServlet 클래스로 교체한다.
- Worker의 execute() 메서드에서 파라미터 타입을 변경한다.
- Worker 구현체를 변경한다.
- Redirect 적용하기
  => BoardAddWorker, BoardDeleteWorker, BoardUpdateWorker
  
# step14 (forward/include, ServletRequest 보관소 응용)
- 오류를 처리할 서블릿 추가한다.
  => ErrorServlet
- 서블릿에 있는 오류 처리 코드를 제거하고 대신에 
  ErrorServlet 으로 포워딩한다.
- ServletRequest 보관소를 사용하여 오류 정보를 ErrorServlet과 공유한다.
- include 응용
  FooterServlet 추가 및 include 
  HeaderServlet 추가 및 include 

# step15 (세션을 활용한 로그인/로그아웃 구현)
- LoginServlet 클래스 추가
  doGet() : 로그인 폼 제공
  doPost() : 로그인 처리
- LogoutServlet 클래스 추가
  doGet() : 세션 무효화처리  
- "Member" Value Object 클래스 추가  
- MemberDao 인터페이스 추가
- MemberMapper.xml 파일 추가
- HeaderServlet 에 로그인 사용의 이름 출력하기

# step16 (쿠키를 이용한 로그인 이메일 저장 구현)
- LoginServlet에 "이메일저장" 기능 추가

# step17 (JSP를 이용하여 서블릿에서 출력 코드를 분리하기)
- LoginServlet에서 로그인폼 출력 코드를 JSP로 분리하기
  => /webapp/auth/LoginForm.jsp

# step18 (JSP를 다른 모든 서블릿(또는 워커)에 적용한다.)
- BoardListWorker에 JSP 적용 => /webapp/board/BoardList.jsp 파일 추가
- BoardDetailWorker에 JSP 적용 => /webapp/board/BoardDetail.jsp 파일 추가  
- FooterServlet을 footer.jsp로 전환
  => FooterServlet 클래스 삭제
- HeaderServlet을 header.jsp로 전환
  => HeaderServlet 클래스 삭제
- ErrorServlet을 error.jsp로 전환
  => ErrorServlet 클래스 삭제

# step19 (로그인/로그아웃 서블릿을 Worker 객체로 전환)
- LoginServlet 클래스를 LoginWorker 클래스로 변경
  => /webapp/auth/LoginForm.jsp에서 <form>의 action 속성 변경
  => header.jsp 에서 링크 변경
  => LoginServlet 클래스 삭제
- LogoutServlet 클래스를 LogoutWorker 클래스로 변경 
  => LogoutServlet 클래스 삭제

# step20 (프런트 컨트롤러에 페이지 컨트롤러의 공통 기능 추가)
- 페이지 컨트롤러에서 프런트 컨트롤러로 오류 페이지 포워딩 기능 이전
  => 모든 페이지 컨트롤러를 변경한다.

# step21 (프런트 컨트롤러에 페이지 컨트롤러의 공통 기능 추가2)
- JSP로 포워딩 하는 기능을 프런트 컨트롤러로 이전한다.
- Refresh, Redirect 기능을 프런트 컨트롤러로 이전한다.
- Worker 인터페이스의 execute() 메서드 리턴 타입 변경 
  void execute(...) ---> String execute(...)
  
# step22 (특정 업무와 관련된 Worker들을 한 Worker에 모으기)
- controller 패키지 생성
- controller 패키지 생성- BoardListWorker, BoardAddWorker, BoardUpdateWorker, BoardDeleteWorker, 
  BoardDetailWorker ----> BoardController 클래스로 모은다.
- LoginWorker, LogoutWorker ----> AuthController 클래스로 모은다. 
- DispatcherServlet 변경하라!
  => 요청이 들어오면,
     1) Spring IoC 컨테이너에서 @Component 애노테이션이 붙은 객체를 꺼낸다.
     2) 해당 객체에서 @RequestMapping 이 붙은 메서드를 꺼낸다.
     3) 그 메서드 중에서 요청하는 서블릿 경로와 일치하는 메서드를 꺼낸다.
     4) 그 메서드를 호출한다. 

# step23 (요청 핸들러의 파라미터를 자동 인식하여 처리하기)
- DispatcherServlet 변경
  => 페이지 컨트롤러의 메서드를 분석하여 그 메서드가 원하는 값을
     찾아서 넘겨준다.
- 각 페이지 컨트롤러의 메서드를 변경
  => 필요한 경우에만 파라미터 값을 받는다.

# step24 (요청 파라미터 값을 자동으로 받기)
- DispatcherServlet 클래스 변경
  => 요청 핸들러의 파라미터가 자바 원시 타입인 경우,
     @RequestParam 애노테이션을 분석하여 그에 해당하는 파라미터 값을 전달한다.
- 페이지 컨트롤러의 요청 핸들러 변경
  => 요청 파라미터 값을 직접 받는다.
    
# step25 (스프링 WebMVC의 DispatcherServlet 적용)
- 기존의 DispatcherServlet 클래스 삭제
- web.xml 변경
  => 기존의 ContextLoaderListener 설정을 주석으로 막는다.
  => 프런트 컨트롤러를 스프링 WebMVC의 DispatcherServlet으로 변경
- DispatcherServlet이 사용할 스프링 설정 파일을 만든다.
  => /WEB-INF/dispatcher-servlet.xml 파일 생성
  => /WEB-INF/jdbc.properties 파일 
- 기존 파일 제거
  => /src/main/resources/conf/* 파일 및 폴더 제거

# step26 (스프링의 기능을 활용하여 Page Controller 변경)
- 기존의 페이지 컨트롤러를 변경
 
# step27 (JSP 파일에 JSTL & EL 적용)
- JSTL 의존 라이브러리 추가 
- /webapp/auth/*.jsp 변경 
  /webapp/board/*.jsp 변경

# step28 (JSON 데이터를 리턴하는 페이지 컨트롤러를 생성)
[1단계]
기존의 프론트 컨트로러가 관리하는 객체 중에서 
Dababase, MyBatis, DAO, Service 등 페이지 컨트롤러가 사용하는 기반이 되는 객체를
분리하여 관리한다.
  => ContextLoaderListener를 추가한다!
     1) web.xml 파일에 리스너 태그 추가
     2) application-context.xml 파일을 /WEB-INF/ 폴더에 둔다.
        => 이 파일에는 DAO, Mybatis, Database, Service 객체만 설정한다. 
  => 기존 DispatcherServlet의 스프링 설정 파일에서 
     위에서 관리하기로 한 객체를 제외시킨다. 

[2단계]
1) JSON 데이터를 처리할 페이지 컨트롤러를 만든다.
  => example.controller.json.BoardController
  => example.controller.json.AuthController
2) web.xml에 *.json 요청을 처리할 프론트 컨트롤러를 추가한다.
  => 물론 그 프론트 컨트롤러를 위한 스프링 설정 파일도 추가한다.
  => /WEB-INF/json-servlet.xml 
  => /WEB-INF/dispatcher-servlet.xml에서 json 패키지의 클래스를 제외한다.

[3단계]
1) 페이지 컨트롤러를 JSON 문자열을 리턴하도록 변경한다.
   => google-gson라이브러리 추가
   
[4단계]
1) boardApp.html 생성
   => AJAX를 이용하여 JSON 문자열을 받은 다음 테이블을 동적으로 갱신한다.
2) boardForm.html 생성
   => AJAX를 이용하여 게시물 등록, 조회, 변경, 삭제 처리
3) example.controller.json.BoardController 클래스 변경 
 
# step29 (로그인 + AJAX + JSON)  
1) AuthController 에 AJAX 적용
2) LoginForm.html 생성
   - 쿠키 처리

# step30 (자바스크립트 코드의 라이브러리화)
1) HTML과 자바스크립트를 분리
   authApp.html  -> authApp.html + authApp.js
   boardApp.html -> boardApp.html + boardApp.js
   boardForm.html -> boardForm.html + boardForm.js

2) AJAX 요청 코드를 공통 함수로 만든다.
   function ajax({요청정보}) {}
   => webapp/js/bit.js에 공통 함수를 놓는다.

3) delete 할 때 암호 입력하도록 처리
   => example.controller.json.BoardController 코드 변경
 
 
# step31 (함수 라이브러리를 다른 라이브러리와 구분하기 위해 객체에 넣기)
1) bit 객체를 생성하여 그 객체에 함수는 넣는다.
2) $ 변수 사용

# step32 (bit 객체를 함수 객체로 만들어 기타 기능 추가하기)
1) bit 함수에 이벤트 리스너 등록과 DOM API 기능 추가 

# step33 (bit 라이브러리를 jQuery 라이브러리로 대체)

# step34 (자바스크립트 패키지 관리자를 이용한 라이브러리 관리)
- npm 또는 bower를 이용하여 라이브러리 관리
- 요즘 추세는 npm으로 단일화하려 한다.
1) npm init
   => 패키지 관리 설정 파일 자동 생성
   => ../webapp/ 폴더에서 명령을 실행한다.
   => /webapp/package.json
2) npm install 라이브러리명 --save
   => 최신 버전 의존 라이브러리 파일 다운로드 및 package.json에 등록하기
   => 예) npm install bootstrap --save
3) 이전 버전 가져오기
   => npm install 라이브러리@이전버전 --save
   => npm install jquery@1.12.4
- 자바스크립트 라이브러리를 서버에 다운로드 해야 하는가?
  그냥 CDN을 이용하여 외부 링크를 걸면 안되는가?
  => 보안 상 외부 사이트에 링크를 걸지 못할 경우가 있다.
  => 하이브리드 앱을 만들 때는 반드시 모든 파일을 패킹해야 한다.
  
# step35 (JSON 출력 ViewResolver)
1) Object --> JSON String 자동 변환 뷰 리졸버 설치
  => 뷰리졸버 설치
  => JSON 스트링 변환 뷰 설치
  => 페이지 컨트롤러의 리턴 코드를 변경
     => 객체를 그냥 리턴하라!
  
# step36 (JSON 결과를 저장할 VO를 만들어 사용)
- JsonResult 클래스 정의
- 페이지 컨트롤러의 리턴 값으로 사용   

# step37 (RESTful 서버 주소를 별도로 관리)
- 백엔드와 프론트엔드를 분리할 경우를 대비하여
  백엔드 서버의 주소를 별도의 자바스크립트 변수에 저장한다.
  => webapp/js/common.js
- 게시물 상세보기 폼에 로그인 사용자 정보 로딩하는 기능 추가
  => boardForm.html, boardForm.js

# step38 (JSP 감추기)
- 외부에서 직접 JSP를 요청하지 못하도록 한다.
  JSP는 페이지 컨트롤러가 작업한 후에 호출되어야 한다.
  => webapp/WEB-INF/view 폴더에 옮긴다.
  => JSP파일이 어느 폴더에 있는지 스프링에 알려준다.
     뷰리졸버 교체 
  => InternalResourceViewResolver의 설정에 맞추어 
     페이지 컨트롤러는 JSP 경로를 리턴해야 한다.

# step39 (HTML 템플릿 자바스크립트 라이브러리)
- handlebars 자바스크립트 라이브러리 추가
  => npm install handlebars --save
- /webapp/board/boardApp.html, boardApp.js 변경
  => handlebars 라이브러리 임포트
  => <tr> 태그 생성을 handlebars를 사용하여 처리  

# step40 (파일 업로드 기능 추가)
1) 스프링 설정 파일에 멀티파트 처리 객체 등록
  => /webapp/WEB-INF/dispatcher-servlet.xml
  => /webapp/WEB-INF/json-servlet.xml
    <!-- 멀티파트 요청 데이터를 처리할 객체를 등록한다. -->
    <bean id="multipartResolver"
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 업로드할 수 있는 파일의 최대 크기 설정(바이트) -->
        <property name="maxUploadSize" value="10000000"/>
    </bean>

2) 아파치 fileupload 라이브러리 추가
  => build.gradle 
compile group: 'commons-fileupload', name: 'commons-fileupload', version: '1.3.2'

3) 이클립스 설정 파일 갱신
  => gradle eclipse   : 명령 실행   
  => 이클립에서 프로젝트 리프레시 

4) 게시물 입력 폼에 파일 업로드 항목 추가
  => /webapp/board/form.html

5) 자바 클래스 준비
  => BoardFile.java (VO)
  => BoardFileDao.java (DAO)

6) BoardFileDao를 위한 SQL 맵퍼 파일 준비
  => src/main/resources/sql/BoardFileMapper.xml
  => insert()를 위한 SQL 준비
  
7) 페이지 컨트롤러의 요청 핸들러 변경 
  => BoardController.add() 메서드 변경    

# step41 (Service Layer 추가)
=> 페이지 컨트롤러가 하던 업무 관련 일을 별도의 클래스로 분리한다.
=> BoardController = BoardController + BoardService + DefaultBoardService
   1) service 패키지 생성
   2) BoardService 인터페이스 추가
   3) service.impl 패키지 생성
   4) DefaultBoardService 클래스 추가
=> BoardService의 데이터 변경 메서드에 대해 트랜잭션으로 묶는다.
   => 스프링 설정 파일에 트랜잭션을 적용할 메서드의 규칙을 지정한다.
   => application-context.xml 파일에 다음 추가
    <!-- 트랜잭션 관리자 등록 -->
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource"/>
    </bean>
    
    <!-- 서비스 메서드에 적용할 AOP 필터 선언 
         => 필터 클래스를 만들지 않았는데?
         => 스프링에서 트랜잭션을 위해 미리 만들어 놓은 AOP 필터(advice) 객체이다.
            개발자가 따로 필터 클래스를 만들 필요가 없다. 
    -->
    <tx:advice id="txAdvice" transaction-manager="txManager">
        <tx:attributes>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="*"/>
        </tx:attributes>
    </tx:advice>
    
    <!-- 스프링 AOP 기능 활성화 시킨다. -->
    <aop:aspectj-autoproxy/>
    
    <!-- 포인트 컷(특정 조건에 해당하는 메서드에 대해)으로 AOP 필터 적용 -->
    <aop:config>
        <!-- 포인트 컷 선언 => 필터를 적용할 대상 클래스와 메서드를 지정 -->
        <aop:pointcut id="serviceOperationPointCut" 
                      expression="execution(* *..service.impl..*(..))"/>
        <!-- 포인터 컷과 어드바이스를 연결 
             => 대상 메서드를 만나면 txAdvice 규칙에 따라 필터를 실행하라!
             => 즉 대상 메서드를 호출할 때 마다 txAdvice 규칙에 정의된 대로
                트랜잭션을 다뤄라! 
        -->
        <aop:advisor advice-ref="txAdvice" 
                     pointcut-ref="serviceOperationPointCut"/>
    </aop:config>     

# step42 (게시판 목록 화면에 페이징 기능 추가)
- BoardController.java, BoardService.java, DefaultBoardService.java, 
  BoardDao.java, BoardMapper.xml 변경
- boardApp.html, boardApp.js 변경




       