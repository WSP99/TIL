- 컴퓨터의 네트워크는 호스트 사이의 연결 거리를 기준으로 LAN, MAN, WAN으로 구분할 수 있다.

## 01. LAN (Local Area Network)
 - 단일건물이나 학교같은 소규모 지역에 위치하는 호스트로 구성된 네트워크이며, **브로드캐스팅 방식**으로 전송한다.  
   (브로드캐스팅 : 일대다 통신의 대표적인 유형으로, 네트워크에 연결된 모든 호스트에 데이터를 전송할 수 있는 방식이다.)
 - LAN 환경에서 호스트를 연결하는 방식에는 버스형과 링형이 있다.
  - ## A. 버스형
    - ![image](https://user-images.githubusercontent.com/29484377/138344347-e40b90d4-0bd2-4194-a1ff-d99e9868fc7a.png)
    - 공유 버스 하나에 여러 호스트를 직접 연결하는 방식이다.
    - 둘 이상의 호스트가 동시에 공유버스에 전송하려고 하면 충돌이 발생하는데, 처리 방식에 따라서 두가지로 나눠진다.
    - 이더넷(Ethernet) : 충돌 발생을 허용하나, 충돌 후 문제를 해결하는 사후 해결 방식이다. (CSMA/CD)
    - 무선랜 : 충돌 발생 자체를 허용하지 않는 방식이다. (CSMA/CA)
  - ## B. 링형
    -![image](https://user-images.githubusercontent.com/29484377/138344970-9bf8df30-53ed-4d63-8cd3-a5765d49a98c.png)
    - 전송 호스트의 연결이 순환구조인 링 형태이다.
    - 특정 방향으로만 데이터가 전송되며, 한 바퀴를 돌면 송신호스트에게 돌아온다.   
    - 링형도 마찬가지로 충돌이 발생할 수 있는데 **토큰**이라는 제어 프레임을 사용하여 충돌 가능성을 차단한다.
      특정 호스트가 발언권을 얻어야만 데이터를 송신할 수 있다.
 - ## C. 스타형
    -![image](https://user-images.githubusercontent.com/29484377/138345179-1495c199-a966-410c-87b2-7cd87471539a.png)
    - 중앙집중식 구조, 중앙의 교환장비가 데이터 경로를 개설하고 유도함. 
 - ## D. 트리형
    -![image](https://user-images.githubusercontent.com/29484377/138345267-f535aa3f-3598-46d4-b0a2-a34895362c7e.png) 
    - 지역과 거리에 따라 연결하므로 통신선로의 총 경로가 가장 짧다. 
    - Multipoint 데이터 통신망이다.
 - ## E. 메시(mesh)형
    -![image](https://user-images.githubusercontent.com/29484377/138345403-bdf89a34-feba-433f-9869-e3ab02053380.png) 
    - 네트워크의 모든 노드를 상호간 연결한다.
    - 통신 선로가 가장 긴 네트워크 구조이며, 공중통신망에서 많이 사용한다.

## 02. MAN (Metropolitan Area Network, 도시권 통신망)
- LAN 보다 조금 더 큰 규모를 지원하며, 한 도시의 네트워크까지 연결이 가능하다. (10km ~ 수백 km)
![image](https://user-images.githubusercontent.com/29484377/138345794-ec6f3fc9-fb11-41d9-b429-e30f9f925249.png)
- DQDB 구조 지원 : 두 개의 단방향 선로가 존재함.

## 03. WAN(Wide Area Network)
![image](https://user-images.githubusercontent.com/29484377/138346353-9d5702e1-22fd-4005-b059-e7e5f84a3f71.png)
- 국가 이상의 넓은 지역을 지원하는 네트워크 구조
- 점대점(Point to Point)로 연결되어 있음. (브로드 캐스팅과 반대)
- 모두에게 데이터를 보내는 것이 아니라 특정 호스트에게만 데이터를 보내야 하므로, 교환 기능이 필요하다.
