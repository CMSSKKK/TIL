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
  
* 톰캣 설치하기

   https://tomcat.apache.org/



* linux / putty 명령어 내용 

`vi 파일이름.확장자` : 이름에 해당하는 파일을 생성 또는 실행 

`i` : 편집기 이용(코드 수정이 가능) 

`esc + :wq  `    :     `esc`를 누르면 편집이 종료되고 커맨드라인에 명령어가 입력할 수 있게 된다.
							  `:wq `입력 후 엔터치면 파일 저장 및 편집기 종료

` gg`  : vi 편집기에서 맨 첫줄로 이동 

`shift + v + g` : 전체 선택 

`y ` :복사 ( 또는 전체 선택 후 복사) 

`d` : 삭제 ( 또는 전체 선택 후 삭제) 

`ctrl + Insert` :  윈도우에서 리눅스로 복사하고 싶을때 윈도우 코드를 드래그 후 누르면 복사 

`shift + insert `복사한 것을 리눅스 커맨드 창이나 vi 편집기에서 사용 시 붙여넣기

>  (https://bbeomgeun.tistory.com/41 )

