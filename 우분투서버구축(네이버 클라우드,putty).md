# putty로 네이버 클라우드  

# 우분투 서버 구축 및 설정

:bulb: 네이버 클라우드 콘솔에서 서버 생성





:bulb: putty 실행

* *host name* `root@ip address` *port* `5000`

* *Session* 설정 및 저장
* *password* 입력하기  
  * 비밀번호 확인은 console에서 *관리자 비밀번호 확인*에 인증키 첨부로 확인
* `sudo apt update`

* `sudo apt upgrade`



[Ubuntu 16.04 apt 'E: Unmet dependencies' 에러]

```
sudo apt-get -o Dpkg::Options::="--force-overwrite" install --fix-broken
```

* 위 명령어 입력하면 해결 ! 

>  (https://koodev.tistory.com/61)



* 자바 설치하기 `sudo apt install openjdk-8-jdk`
  * 설치 확인 `java -version` , `javac -version`
  * 설치 위치 `usr/lib/jvm/java-8-openjdk-amd64#`

