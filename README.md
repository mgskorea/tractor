# tractor 설치법

### Pixar license 서버설치
- 픽사 라이센스 설치파일을 다운로드 합니다. https://renderman.pixar.com/forum/download.php
- 트렉터 라이센스를 발급 받습니다. : https://renderman.pixar.com/forum/entitlement.php
![activation](https://user-images.githubusercontent.com/1149996/45874861-ed7c6580-bdd0-11e8-836a-a619a4875b72.png)
    - Hostname은 `hostname` 명령어로 알아 낼 수 있습니다.
	- HostID는 Mac address입니다. `ifconfig` 명령으로 알아낼 수 있습니다.

- 서버 홈디렉토리에 위 파일을 복사합니다.
- 라이센스파일을 /opt/pixar에 복사합니다.
```
# cp ~/pixar.license /opt/pixar
```
- PixarLicense를 설치합니다.
```
# cd ~
# rpm -i ./PixarLicense-LA-21.0_1634130-linuxRHEL6_gcc44icc150.x86_64.rpm
```

- 라이센스 서버를 설치하고 잘 실행되는지 확인한다.
```
# cd /opt/pixar/PixarLicense-LA-21.0
# sh linux_installService.sh
# PixarLicenseServer
```

- 픽사 라이센스 서버를 설치하면 자동으로 /etc/init.d/pixarlicenseserver 파일이 생성됩니다.
- 위 파일을 이용해서 아래 명령어를 이용할 수 있습니다.
```
# /etc/init.d/pixarlicenseserver start
# /etc/init.d/pixarlicenseserver stop
# /etc/init.d/pixarlicenseserver restart
```

### Tractor Engine 설치

### Tractor Blade 설치
