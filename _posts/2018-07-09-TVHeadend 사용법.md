---
layout : post
title : TVHeadend 설치 및 사용법
date : 2018-07-09 11:20:23
tags:
- 서버
category: Server
---

# Intro
<br>
이 포스트는 <a href="https://cozy-ho.github.io/server/2018/07/05/IPTV_ip%EC%A3%BC%EC%86%8C-%EC%8A%A4%EC%BA%94%ED%95%98%EA%B8%B0.html" target="_blank"> OMVP 설치 및 사용법 & IPTV 주소 스캔하기 </a>포스트에 의존합니다. 이전 포스트를 먼저 보시는걸 추천합니다.

---

`TVHeadend`란 IPTV나 TV 수신카드의 데이터를 재전송해, 다른 클라이언트(TV가 아닌)에서 TV시청이 가능하게 해주는 서비스이다.

설치하기에앞서 우분투 컨테이너를 하나 생성해 준다. Host에 서비스들을 바로 설치하는건 보안상 좋지않기때문에 서비스별로 분리하는걸 기본으로 한다. 나는 `Ubuntu_server-16.04`를 설치했다.

---

# TVHeadend 설치하기
<br>
우분투 설치후 커맨드 창에서 다음 명령어를 입력해준다.
<br><br>

```
$sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 379CE192D401AB61

$echo "deb https://dl.bintray.com/tvheadend/deb xenial stable-4.2" | sudo tee -a /etc/apt/sources.list

$sudo apt-get update

$sudo apt-get install tvheadend
```

역시 우분투. 쉽고 빠르다.


---

# 기본 설정
<br>
설치 후 `설치 IP:9981`에 접속하면 다음과 같은 웹 설정 페이지가 나온다.
<br><br>

![img1](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-01.png?raw=true)
<br>

언어설정이다. `EPG Language`에서 `Language 1:`을 한국어로, 나머지는 영어로 하면된다.<br>언어를 바꾸고 이 창이 또 뜰 수 있는데 그냥 `Next`클릭하면 된다.
<br><br>

![img2](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-02.png?raw=true)
<br>

관리자 설정 및 접속 설정 페이지다.<br>`0.0.0.0/0`으로 해두면 어디서든 연결이 가능하다.*포트포워딩은 해야한다.*

다음은 로그인시 필요한 관리자 ID&PW. 입력후 Next.
<br><br>

![img3](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-03.png?raw=true)
<br>

그 뒤로는 아무설정도 하지말고 그냥 쭉 Next, Next 하고 완료한다.

설정이 끝나면 초기에 입력한 ID,PW로 로그인한다.

다음, 설정에서 Configuration > General > base 에서 다음과같이 설정한다.
<br><br>

![img4](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-04.png?raw=true)
<br>

![img5](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-05.png?raw=true)
<br>

Theme은 취향에 맞게 선택하면 된다.

---

# 채널 등록하기
<br>

여기서부터는 나는 실패했지만.. 일단 방법을 적어둔다.

네트워크 추가를 위해 Configuration > DVB Inputs > Networks에서 `Add`버튼을 눌러준다.
<br><br>

![img6](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-06.png?raw=true)
<br>

그리고 드롭리스트중에서 다음을 선택한다.
<br><br>

![img7](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-07.png?raw=true)
<br>

그리고 다음과 같이 양식을 채운다.<br>URL에는 m3u파일의 절대경로를 적어둔다.
<br><br>

![img8](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-08.png?raw=true)
<br>

![img9](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-09.png?raw=true)
<br>

반드시 `Accept zero value for TSID` 를 체크하고 `Create`를 누르자. 누르지 않을경우 URL에 복사한 m3u파일의 경로를 file:// 뒤에 입력하므로 채널등록이 제대로 되지 않는다.
<br><br>

![img10](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-10.png?raw=true)
<br>

Configuration > DVB Inputs > Muxes 로 이동하면 위의 이미지처럼 채널이 스캔된다.
<br><br>

![img11](https://github.com/Cozy-Ho/Cozy-Ho.github.io/blob/master/images/_post-18-07-09-11.png?raw=true)
<br>

채널의 앞, 뒤 체크박스를 모두 체크하고 `Save` 버튼을 누른다.

---

# 끗
<br>

모두 끝났다. 구글이 시키는대로 다 했지만 `Scan result`가 모두`FAIL` 로 뜨며 나오지 않았다.. 네트워크 문제인지 설정 문제인지 감이 잡히지 않는다ㅠㅠ

성공하신 분들은 Emby 설정 페이지에서 플러그인 > TVheadend를 찾아 설치하면 TV시청이 가능하다. 굳이 Emby가 아니라도 `Kodi`로도 시청이 가능하다.

혹시 나중에라도 성공하게 된다면 이 포스트를 업데이트 하겠다.