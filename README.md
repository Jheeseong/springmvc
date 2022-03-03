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

# v1.0 3/2
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
