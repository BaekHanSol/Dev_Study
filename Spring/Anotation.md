@Autowired
--
      의존성 주입. 상황의 타입에 맞는 IoC(Inversion Of Control)컨테이너 안에 존재하는 Bean을 자동으로 주입한다.
      option - required = true/false
      참고) 동일한 타입의 빈이 여러개인 경우
      @Primary : 빈의 우선권을 가지게 하여 해당 Bean을 선택되도록 함
      @Qualifier : 빈의 id값으로 어떤 bean을 사용할 것인지 지정. ex) @Qualifier("선택될 bean의 class명")
      동일 타입의 모든 bean 리스트로 받기 - @PostConstruct : 의존성 주입 후 한번 실행 되는 bean의 생성주기
--
