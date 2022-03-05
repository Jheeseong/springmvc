# springmvc
# v1.0 3/2
# Logging
**로그 선언**
- private Logger log = LoggerFactory.getLogger(getClass());
- private static final Logger log = LoggerFactory.getLogger(Xxx.class)
- @Slf4j : 롬복 사용

**로그 호출**
- log.info("hello")
- System.sout.println("hello")
- 시스템 콘솔 출력하는 것 보다 로그 사용이 더 많은 장점이 존재 

**LogTest**

    @RestController
    public class LogTest {

        private final Logger log = LoggerFactory.getLogger(getClass());

        @RequestMapping("/log-test")
        public String logTest() {

            String name = "LOG-TEST";

            log.trace("trace log={}",name);
            log.debug("debug log={}",name);
            log.info("info log={}",name);
            log.warn("warn log={}",name);
            log.error("error log={}",name);

            return "ok";
        }
    }
    
- @RestController
  - @Controller 반환 값이 String일 경우 뷰 이름으로 인식하여 찾는 뷰가 렌더링
  - 반환 값으로 뷰를 찾는 것이 아닌, Http 메시지 바디에 바로 입력

**로그 장점**
- 쓰레드 정보, 클래스 이름 같은 여러 정보를 함께 확인 가능하며, 출력 모양 조정이 가능
- 상황에 맞게 로그 레벨을 조절 가능
- 시스템 아웃 콘솔 출력 뿐만 아니라, 파일, 네트워크 등, 로그 별도의 위치에 남기는 것이 가능
- 성능이 system.out보다 좋음

# v1.1 3/3
# 요청 매핑
**MappingController**

    @RestController
    @Slf4j
    public class MappingController {

    /*
     * 기본 요청
     * 둘 다 허용 /hello-basic, /hello-basic/
     * Http 메서드 모두 허용 - GET,HEAD,PUT,PATCH,DELETE
     * */
        @RequestMapping("/hello-basic")
        public String helloBasic() {
            log.info("helloBasic");
            return "ok";
        }
        
**매핑 정보**
- @RestController
  - @Controller는 반환 값이 String이면 뷰 이름으로 인식하여 뷰를 찾고 렌더링
  - @RestController는 반환 값으로 뷰를 찾는 것이 아닌, Http 메시지 바디로 바로 입력. 따라서 실행 결과로 OK 메세지 받기 가능
- @RequestMapping("/hello-basic")
  - /hello-basic URL 호출이 오면 이 메서드가 실행되도록 매핑
  - 대부분 속성을 배열로 제공함으로 {"/hello-basic","/hello-go"} 식의 설정이 가능

**Http 메서드**
- @RequestMapping에 method 속성으로 Http 메서드를 지정하지 않으면 Http 메서드와 무관하게 호출
- 모두 허용(GET,HEAD,POST,PUT,PATCH,DELETE)

**Http 메서드 매핑**

     * method 특징 http 메서드 요청만 허용
     * GET, HEAD, POST, PUT, PATCH, DELETE
    @RequestMapping(value = "/mapping-get-v1", method = RequestMethod.GET)
    public String mappingGetV1() {
        log.info("mappingGetV1");
        return "ok";
    }
    
**Http 메서드 매핑 축약**

     /*
     * method 축약 에노테이션
     * GetMapping
     * PostMapping
     * PutMapping
     * DeleteMapping
     * PatchMapping
     * */
    @GetMapping(value = "/mapping-get-v2")
    public String mappingGetV2() {
        log.info("mappingGetV2");
        return "ok";
    }
    
- Http 메서드를 축약한 애노테이션을 사용하는 것이 더 직관적.

# v1.2 3/4
**PathVariable(경로 변수) 사용**

    /*
     * PathVariable 사용
     * 변수 명이 같으면 생략 가능
     * @PathVariable("userId") String userId -> @PathVariable userId
     * */
    @GetMapping("/mapping/{userId}")
    public String mappingPath(@PathVariable("userId") String userId) {
        log.info("mappingPath userId = {}", userId);
        return "ok";
    }
    
- @RequestMapping은 URL 경로를 탬플릿화 할 수 있는데, @PathVariable을 사용하면 매칭 되는 부분을 편리하게 조회 가능
- @PathVariable의 이름과 파라미터 이름이 같을 경우 생략 가능.

**PathVariable 다중 사용**

    /*
     * 다중 pathVariable 사용
     * */
    @GetMapping("/mapping/users/{userId}/orders/{orderId}")
    public String multiMappingPath(@PathVariable String userId, @PathVariable Long orderId) {
        log.info("mappingPath userId = {}, orderId = {}", userId, orderId);
        return "ok";
    }
    
**특정 파라미터 조건 매핑**

    /*
     * 특정 파라미터 조건 매핑
     * 파라미터로 추가 매핑
     * params="mode",
     * params="!mode"
     * params="mode=debug"
     * params="mode!=debug" (! = )
     * params = {"mode=debug","data=good"}
     */
    @GetMapping(value = "/mapping-param", params = "mode=debug")
    public String mappingParam() {
        log.info("mappingParam");
        return "ok";
    }
    
**특정 헤더 조건 매핑**

    /*
     * 특정 헤더로 추가 매핑
     * headers="mode",
     * headers="!mode"
     * headers="mode=debug"
     * headers="mode!=debug" (! = )
     */
    @GetMapping(value = "/mapping-header", headers = "mode=debug")
    public String mappingHeader() {
        log.info("mappingHeader");
        return "ok";
    }
    
**미디어 타입 조건 매핑-Http 요청(content-type,consume)**

    /*
     * Content-Type 헤더 기반 추가 매핑 Media Type
     * consumes="application/json"
     * consumes="!application/json"
     * consumes="application/*"
     * consumes="*\/*"
     * MediaType.APPLICATION_JSON_VALUE
     */
    @PostMapping(value = "/mapping-consume", consumes = MediaType.APPLICATION_JSON_VALUE)
    public String mappingConsumes() {
        log.info("mappingConsumes");
        return "ok";
    }
    
- Http 요청의 Content-Type 헤더를 기반으로 미디어 타입으로 매핑, 맞지 않은 경우 415 상태코드 반환.

 **미디어 타입 조건 매핑 - http 요청(Accept,produce)**
 
     /* * Accept 헤더 기반 Media Type
     * produces = "text/html"
     * produces = "!text/html"
     * produces = "text/*"
     * produces = "*\/*"
     */
    @PostMapping(value = "/mapping-produce", produces = MediaType.TEXT_HTML_VALUE)
    public String mappingProduces() {
        log.info("mappingProduces");
        return "ok";
    }
    
- Accept 헤더를 기반으로 미디어 타입으로 매핑, 맞지 않을 경우, 406 상태코드 반환.

# API 요청 매핑
**MapingClassController**

    @RestController
    @RequestMapping("/mapping/users")
    public class MappingClassController {

        @GetMapping
        public String users() {
            return "get users";
        }

        @PostMapping
        public String addUsers() {
            return "add users";
        }

        @GetMapping("/{userId}")
        public String SearchUser(@PathVariable String userId) {
            return "search user = " + userId;
        }

        @PatchMapping("/{userId}")
        public String updateUser(@PathVariable String userId) {
            return "update user = " + userId;
        }

        @DeleteMapping("/{userId}")
        public String deleteUser(@PathVariable String userId) {
            return "delete user = " + userId;
        }
    }
    
- 회원 목록 조회 : GET /mapping/users
- 회원 등록 : POST /mapping/users
- 회원 조회 : GET /mapping/users/{userId}
- 회원 수정 : PATCH /mapping/users/{userId}
- 회원 삭제 : DELETE /mapping/users/{userId}

# v1.3 3/5
# Http 요청 - 기본,헤더 조회

**RequestHeaderController**

    @Slf4j
    @RestController
    public class RequestHeaderController {

        @RequestMapping("/headers")
        public String headers(HttpServletRequest request,
                          HttpServletResponse response,
                          HttpMethod httpMethod,
                          Locale locale,
                          @RequestHeader MultiValueMap<String, String> headerMap,
                          @RequestHeader("host") String host,
                          @CookieValue(value = "myCookie", required = false)
                          String cookie) {

            log.info("request={}", request);
            log.info("response={}", response);
            log.info("httpMethod={}", httpMethod);
            log.info("locale={}", locale);
            log.info("headerMap={}", headerMap);
            log.info("header host={}", host);
            log.info("myCookie={}", cookie);

            return "ok";
        }
    }
    
- HttpMethod : Http 메서드를 조회
- Locale : Locale 정보를 조회
- @RequestHeader MultiValueMap<String, String> headerMap : 모든 Http 헤더 정보를 map 형식으로 조회
- @RequestHeader("host") String host : 특정 Http 헤더를 조회
- 속성
  - 필수 값 여부 : required
  - 기본 값 속성 : defaultValue
- @CookieValue(value = "myCookie", required = false) String cookie : 특정 쿠키를 조회한다.
- 속성
  - 필수 값 여부: required
  - 기본 값: defaultValue
- MultiValueMap
  - Map과 유사한데, 하나의 키에 여러 값을 받을 수 있음.
  - Http header, Http 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용

# Http 요청 파라미터 - 쿼리 파라미터, HTML Form
- HttpServletRequest 와 request.getParameter()을 사용하여 파라미터 조회 가능

**클라이언트에서 서버로 여청 데이터를 전달 시 방법**

- GET - 쿼리 파라미터
  - /url?username=hello&age=20
  - 메시지 바디 없이, url의 쿼리 파라미터에 데이터를 포함해서 전달
- POST - HTML Form
  - content-type : application/x-www-form-urlencoded
  - 메시지 바디에 쿼리 파라미터 형식으로 전달
- HTTP message body
  - http API에서 주로 사용(JSON,XML,TEXT)
  - 데이터 형식은 주로 JSON

**RequestParamV1**

    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        log.info("username = {}, age = {}", username, age);

        response.getWriter().write("ok");
    }
    
- request.getParameter() : HttpServletRequest가 제공하는 방식으로 요청 파라미터를 조회
- GET 실행 : http://localhost:8080/request-param-v1?username=hello&age=20

**RequestParamV2**

    @ResponseBody
    @RequestMapping("/request-param-v2")
    public String requestParamV2(@RequestParam("username") String memberName,
                                 @RequestParam("age") int memberAge) {
        log.info("username = {}, age = {}", memberName, memberAge);
        return "ok";
    }
    
- @RequestParam : 파라미터 이름으로 바인딩
- @ResponseBody : View 조회를 무시하고, Http message body에 직접 해당 내용 입력
- @RequestParam의 name(value) 속성이 파라미터 이름으로 사용
  - @RequestParam("username") String memberName
  - -> request.getParameter("username")
