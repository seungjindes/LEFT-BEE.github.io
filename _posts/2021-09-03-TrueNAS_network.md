---
title : "TrueNas를 통한 미디어서버 구축"
categories : "network"
--- 


## TrueNAS Server

클라우드로 데이터를 저장할 수 있는 서버를 구성하게 된 과정과 사용방법 그리고 문제점을 이어서 기술한다

![image](https://user-images.githubusercontent.com/65720894/131797453-c315c019-ec63-409b-9328-6874b17cc048.png)

서버를 구성하는데 있어 대표적인 서버 운영체계인 오픈된 NAS solution을 선택하였다. 그중에서도 freeNAS의 최신버전인 TrueNAS를 사용하였다 
이를 선택한 이유로는 trueNAS core가 zfs라는 파일시스템을 사용하는 freebsd기반이라는 것이다.
zfs 현존하는 파일시스템중 최고라는 수식어가 붙을만큼 훌룡한 파일처리를 보여주고 바로 이 zfs을
지원하는 os가 freebsd이다

zfs는 데이터 무결성을 보장하는 여러 기능을 제공하는 등 수준 높은 툴을 제공할 뿐만아니라 UI가 
매우 잘 만들어져있어 처음 이용하는 입장에서도 다루기 매우 쉬웠다 또한 매우 유명한 
오픈소스이기 때문에 관련되어 많은 정보를 얻을 수 있다는 장점또한 있었다.

하지만 RAM을 많이 차지한다는 단점이 있다 이는 연구실의 서버가 감당가능하다고 판단하여 별 문제 없다고 판단하였다.

## Server 구성과정

![image](https://user-images.githubusercontent.com/65720894/131851857-0aa2ab35-8637-4efc-a587-e05a42d4679d.png)

pc에 우분투를 설치하듯이 TrueNAS를 설치하면 다음과 같은 화면이 표시되고 할당된 IP가 정장적으로
나타난다면 설치가 완료되었다. 

### 사용법 - login
![image](https://user-images.githubusercontent.com/65720894/131852754-d7c43085-7368-41f7-a4d0-9fbf69161f1e.png)

nas를 실행하고 나서 나온 IP주소로 같은 LAN울 공유하고 있는 pc로 들어가면 다음과 같은 화면이 
표시된다. NAS를 설치할 때 root계정에 대한 비밀번호를 설정하는데 root계정과 해당 비밀번호를 치면
서버를 관리할 수 있는 ui로 넘어가게된다.

### 사용법 - pool생성

![image](https://user-images.githubusercontent.com/65720894/131853304-6c53ff05-5deb-431c-8c46-8d0b8de527e0.png)

서버를 이용하기 전에 pool이라는 것을 생성해줘야 한다 pool을 간단하게 설명하면
zfs파일구조의 최상위 수준의 데이터 계층이며 pool안에서 dataset등을 만들 수 있다.

먼저 nas가 인식하는 디스크를 보면 3.64테비바이트 디스크 4개를 인식하고 현재 미사용중임을
알 수 있다. 여기서 부팅디스크가 기존 hdd가 아니라 usb를 사용했는데 처음 설치하였을때
3.64테비바이트 hdd하나를 전부 부팅디스크로 이용하니 다른 용도로 사용할 수 없었다   

이를 해결하기 위해 파티션을 나눠 사용하려 했으나 NAS의 설계가 할 수 없다하여 usb를 부팅
디스크로 만들어 꽂은 다음 사용하는 방식을 채택하게 되었다.   

이제 pool을 만들게 되는데 4개의 hdd를 모두 pool에 포함시켜서 데이터마다 디스크를 구분하는 일이
없도록 만들었다. 그 다음 데이터의 저장방식을 선택하였는데 어떤 raid종류를 선택하냐에 따라 
사용가능한 데이터 공간의 크기가 달라지게 된다 본 서버의 사용용도는 공유파일을 저장하는것 이기
때문에 그렇게 중요한 파일은 저장되지 않을 것이라 생각하여 저장장치하나를 전부 사용하는
raid 1방식을 선택하였다 

### 사용법- dataset생성

![image](https://user-images.githubusercontent.com/65720894/131857251-4e1287b8-9061-4f4e-8fdb-bd7ec6c0aefe.png)

pool안에서 zfs가 파일단위로 관리하는 공간인 dataset을 만드는 과정이다 여러 설정들은
필요에따라 선택하면 된다.

### 사용법 -user 생성
![image](https://user-images.githubusercontent.com/65720894/131857385-5264f009-dbfa-4f8f-bc56-98a83ce7ecb6.png)

root계정은 오로지 관리를 위한 계정이고 사용자계정을 따로 만들어 주어야한다 이름과 패스워드
전부 milab으로 설정해주었다. group 또한 원한다면 설정가능하다.

그 다음은 home directory를 성정하는 것인데 기존에 만든 dataset을 그대로 이용하였다
일단 모든 권한을 다 허용한 다음 계정을 생성하였다 계정과 관련해서는 본래 
각각의 dataset계정을 생성하고 개개인의 공유폴더를 생성하려 했으나
dataset과 계정을 생성하는데의 불편한점과 어차피 해당 디스크를 대부분 dataset 저장용으로
사용하여 개인 정보가 담긴 파일을 넣을 일이 없을 거라 판단, 따라서 계정하나를 공유하는 방식으로
사용하고자 하였다

![image](https://user-images.githubusercontent.com/65720894/131860124-8f761766-94df-4f04-81a6-0a68ee6146dd.png)

다음으로는 사용할 서비스를 활성화 하는 부분이다 우선적으로 smb 프로토콜을 활용하는 방식을 선
택하였고 smb사용을 위해 ssh접속도 허용하게 했다.

### 사용법 - 공유폴더 설정

![image](https://user-images.githubusercontent.com/65720894/131860911-c4f0cec7-8014-49be-93f3-3807f3fea0e6.png)

만든 데이터셋을 smb를 통해 공유하겠다고 설정하면 이제 기본적인 설정은 끝이다.

### 사용법 - 고정 IP설정

![image](https://user-images.githubusercontent.com/65720894/131861028-79be0175-a3c7-4776-bfd3-0928b91d7588.png)

추가로 처음 TrueNAS를 설정하고 나면 ip를 따로 입력하지 않아도 DHCP를 활용해서 자동으로 ip가 
할당되게 된다 하지만 매번 ip가 바뀌는 것은 원하지 않은 상황이기에 DHCP를 끄고 직접 ip를 
고정시켰다

---------------

### 사용법 - Mac OS연결

![image](https://user-images.githubusercontent.com/65720894/131862164-069459a4-36cb-4a81-9c5d-dbb9d264cdc0.png)

이제 각 os에서의 사용방식인데 finder에서 이동 -서버에연결을 선택하면 다음과 같은 창이 
표시된다 smb를 사용하기로 했기 때문에 smb://ip를 치면 다음과 같이 로그인 창이 뜨게 된다
기존에 만든 milab계정을 통해 로그인 하면 자연스럽게 마운트가 되는 것을 확인 할 수 있다.

### 사용법 - Mac OS 업로드

![image](https://user-images.githubusercontent.com/65720894/131862432-c390276f-4f2b-49f5-8b5c-0a6431cf2f85.png)

실제로 파일을 업로드하는 과정이다

### MacOS 업로드 확인

![image](https://user-images.githubusercontent.com/65720894/131862521-27bd28fd-6c4e-448d-a6b7-5dfb739c70e5.png)

업로드 한것이 반영됐는지 확인하기 위해 설정창에서 shell을 띄운다 그리고 해당 daaset에 
들어가면 업로드한 main.py가 있음을 확인가능하다.

### MacOS수정

![image](https://user-images.githubusercontent.com/65720894/131862722-29ff64d5-08b4-430f-980c-5046e8f52e40.png)

외장하드의 파일을 수정하듯이 하면 바로 수정사항이 반영이 된다.

------------

### 사용법 - Windows

![image](https://user-images.githubusercontent.com/65720894/131862887-f8686256-777f-41dc-a0ff-a3729f169dde.png)

![image](https://user-images.githubusercontent.com/65720894/131863089-d6cb5c5e-ed34-46ae-bb6f-01f99ba69437.png)


다음은 window에서의 사용 방식인데 탐색기 창에서 주소창에 역슬래시 2번 ,IP주소를 입력하면
로그인 화면이 나온다. 이후 로그인을 한 후 아까와 같이 사용하면 된다.


------

### 사용법 -Linux(Ubuntu)

![image](https://user-images.githubusercontent.com/65720894/131863241-9dbb1fc3-f7da-4303-8ac6-ce696ce40823.png)

리눅스 파일창에서 ctrl+L 단축기를 이용하거나 다른 위치를 클릭한 후에 smb://IP를 입력하면
마찬가지로 로그인 가능하고 
![image](https://user-images.githubusercontent.com/65720894/131863582-ac57372a-20be-45e7-b75c-5dfdffb9603f.png)

그대로 사용하면된다.

----

### 사용법 - SFTP(Secure File Transfer Protocol)

![image](https://user-images.githubusercontent.com/65720894/131864759-5b1bed95-bb98-431e-b185-59b1323cd80c.png)


다음은 stfp를 사용한 방식이다 stfp란 기존에 파일을 전송하는 프로토콜인 ftp에서 보안기능이 추가된 프로토콜이며 sftp가 ssh기반으로 동작하는 프로토콜은 아니지만 나스에서 sftp를 사용하기 위해서는
ssh연결이 필요하기 때문에 서비스 탭에서 ssh를 활성화 한 다음 contifure창에서 port번호가 22번인 것을 확인 할 수 있다.

![image](https://user-images.githubusercontent.com/65720894/131864729-35fb8220-ecf3-49d8-bba5-17a95e3bdf3f.png)

간단한 예시로 먼저 선택사항으로 서버와 파일을 송수신할 디렉토리 하나를 만들어준다 그 다음
sftp명령어에 포트번호 22번 , ID , IP를 입력한 후 패스워드를 입력해준다.

ls명령어를 입력하면 sftp예시를 위해 test파일을 하나 만들어 놓은 것을 확인 가능한데
get명령과 더불어 서버의 파일을 입력하면 해당 파일을 다운로드 가능하다.

다른 터미널 창에서도보면 다운로드 된것을 확인 할 수 있다. 해당 방식은 smb보다 불편해 
보일 수는 있지만 smb가 사용이 불가능한 상황에서는 도움이된다 

예를들어 dlpc와 같은 환경에서는 smb가 사용이 힘들면 sftp를 사용해 간단하게 파일을 송수신 할 수 있다.

----

## 문제점
![image](https://user-images.githubusercontent.com/65720894/131865481-037c16ad-1c6a-4f48-9e44-62e736e4d58a.png)

ip가 private범위에 해당되며 LAN내부에서만 구분이 가능하며 LAN외부로 나갈때에는 NAT를 활용해 ip주소를 변환해서 나가게된다 여기서 문제가 발생하는데 같은 LAN이 아닌 다른 곳에서 저희 서버에 접근하려고 하면 WAN에서는 private IP의 패킷을 드랍하기 때문에 먼저 접속을 시도하는 것이 어렵게된다.ㅂ


![image](https://user-images.githubusercontent.com/65720894/131866488-4c282e72-77e3-4d28-95d4-52cb5de915d9.png)

다음 그림과 같이 net table에 외부 ip의 특정 포트와 내부 ip의 특정포트를 NAT table에 고정시키는
port forwarding방식이 존재한다 하지만 이는 공유기 설정을 하는데 있어 관리자 계정을 
가지고 있지 않기에 해결하지 못하였다.






























