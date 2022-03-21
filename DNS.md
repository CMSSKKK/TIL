# DNS는 어떻게 작동할까?

> https://howdns.works/ 를 참고하여 작성하였습니다.

1. 컴퓨터와 여러 장치들은 인터넷에서 IP주소를 통해서  서로를 식별하고 통신한다. 
2. 하지만 사용자의 경우 `10.0.0.1 192.168.1.0 8.8.8.8 `와 같은 IP주소를 기억하기 쉽지 않기 때문에,  `google.com naver.com` 과 같은 단어를 사용한다.
3. 그래서 DNS는 사용자가 입력한 주소`google.com`를 통해서 IP주소를 찾아서 원하는 목적지로 안내한다.
4. 이때 브라우저는 `google.com`의 IP주소를 이미 알고있는지, 캐시를 찾아보고, 없다면 `OS`에게 IP 주소를 찾도록 한다.
5. 이때 브라우저와 OS 모두 캐시에 저장되어있지않다면, OS는 `resolver`를 호출한다.
6. `resolver` 또한 요청을 받으면, 캐시를 먼저 확인한다. 
7. 캐시에서 확인이 되지않는다면, `root 서버`를 통해 확인한다.
8. `resolver`는 보통 `ISP(Internet Service Provider)`이며,모든 `resolver`는 반드시 `root server`의 위치를 알고 있어야 한다.
9. `root 서버`는 `.COM` `TLD(Top-Level Domain)` 서버가 어디에 위치해 있는지 알고있다.
10. `root 서버`는 요청하는 도메인주소를 찾을 수 있는 `.COM` `TLD server`를 가르쳐준다. 그리고 이 정보를 `resolver` 가 저장하여 더 이상 `root server` 에 요청할 일이 없어진다.
11. `root server`는 현재 전세계 13개 있으며, DNS 계층 구조에서 최상단에 위치한다. 이 서버들은 12개의 독립적인 기구들이 관리한다.
12. `root server` 들은 `[letter].root-servers.net`이며 `[letter]` 범위는 A에서 M이다. 하지만 13개 있다고 해서 물리적 서버가 13개인 것은 아니다.
13. 이후 `resolver`는 해당하는 `.COM TLD(Top-Level Domain)` 서버에서 도메인에 대한  권한이 있는 `name server`들을 받는다.
14. `TLDs`는(대부분의 TLD 조직) ICANN(Internet Corporation for Assigned Names and Numbers)에 속한다.
15. `.COM TLD`의 경우 1985년에 생긴 가장 먼저 생겼다. 그리고 현재 가장 큰 TLD이다. 
16. `TLDs`의 다른 유형은 ISO로 된 국가 코드, 자국어로 쓰인 국가 코드가 있으며, `.NET`,`.ORG`와 같은 일반적인 형태도 있다. 또한 `Infrastructures TLDs` 인 `.ARPA`의 경우 반대로 IP 주소를 통해서 도메인 주소를 찾는다.
17. `.COM TLD server`는 `Domain Registrar`의 도움을 통해서 권한있는 `name server`를 찾을 수 있다. 도메인이 구매되면, `Domain Registrar`는 이름을 배정하고 `TLD registry`에 있는 `authoritative name servers` 와 통신한다. 
18. `name server`는 DNS 쿼리(website, email 등)에 대한 답변을 제공한다. 캐시된 값은 없고, 다른 서버와 협력하거나 요청하는 것없이 직접 답변을 제공한다.
19. 일반적으로 모든 도메인에 하나 이상의 `name server`가 연결되어 있다.
20. `Name servers list`중에서 임의의 `authoritative name server`에 쿼리를 요청할 수 있다. 몇몇의 웹사이트의 경우 name servers의 정보를 제공하기도 한다.
21. `name server`를 통해서 `resolver`는 IP 주소를 얻을 수 있다.
22. 이 후 OS가 도메인 주소와 IP 주소를 저장하고, 브라우저에게 IP 주소를 넘겨줌으로써 우리가 요청한 웹페이지와 연결할 수 있다.

* BONUS

  * subdomain은 domain에 접근한 이후에야 접근할 수 있다.
  * `name server`의 도메인이`ns1.domain.com`
  * 우리가 요청하는 도메인 주소가 `domain.com`
  *  `ns1.domain.com`은  `domain.com`의 subdomain이 때문에, 먼저 접근할 수 없다.
  * 그렇다면? `Glue records`가 그 문제를 해결한다.
  *  `.COM TLD` 가 `name servers`의 정보를 줄 때, 서버마다 적어도 하나의 IP address를 같이 전달한다.
  * 이것을 `glue`라고 부른다.  그렇기 때문에 subdomain의 접근을 IP address로 하기 때문에 문제가 생기지 않는다.

  

