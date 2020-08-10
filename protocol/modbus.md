Modbus
=============
* Modbus 프로토콜의 이해
  * 소개
    - 기계들을 자동화하고 제어하는 목적으로 사용되는 PLC(Programable Logic Controller)들과의 통신에 사용할 목적으로 만들어 진다.
    - 온도와 습도를 측정하는 여러 장비들이 모니터링 서버로 현재 상태를 보고할 수 없음
      -> SCADA System에서도 모니터링 서버와 RTU(Remote Terminal Unit)를 연결하기 위해 사용된다.
 
 * 통신과 장비
    * master/slave 기반 protocol
      - 시리얼 통신 : master로 설정된 장비만이 slave로 정보 요청
      - 이더넷 통신 : 네트워크상의 어떤 노드도 정보를 요청할 수 있다.(읽기, 쓰기)
                     응답은 요청정보에 있는 목적 주소 장비만 한다.
                     (브로드캐스트 주소일 때에는 예외, 목적지 주소가 0일때 수신한 모든 노드에서 요청을 처리하며 응답은 없다.)
                     
      - master : wifi나 이동통신 등의 무선 통신 지원
 
 * Frame format
    - modbus RTU : 시리얼 통신망 이용, 이진정보 사용
      start(28bit) + Addr(8bit) + Function(8bit) + Data(Data+length) + CRC(에러체크) + END(28bit)
    - modbus ASCII : 시리얼 통신망, ascii 문자를 넣어 통신
    - modbus TCP ( Modbus + 인터넷 프로토콜 TCP/IP)
      Transaction(2byte) + protocol(2byte) + length(2byte) + unit(1byte) + func(1byte) + PDU(function 1byte + data)
      * unit identifier : modbus 게이트웨이 내부의 RTU장비들을 지칭하는 것으로 ip주소로 인식한다
      PDU : Request, Response, Exception Response. 함수와 연관된 데이터 함수코드로 slave에 보내지는 명령
 
 * 구현
  1) 시리얼 통신 -> 데이터 인코딩 방식에 따라 RTU 혹은 ASCII로 나뉜다
      RS-232 : 가까운 거리에 놓인 장비 1:1 연결
      RS-422 : RS - 232 + 양방향 통신 가능
      RS=485 : 여러 장비를 하나의 망으로 묶음, 하나의 master에서 여러개의 slave와 통신할 수 있다.
  2) IP 기반 -> TCP/UDP 가능. IETF에서 502포트 할당
