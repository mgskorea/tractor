# tractor 설치법

#### Pixar license 서버설치
- 트렉터 라이센스 설치파일을 다운로드 한다. https://renderman.pixar.com/forum/download.php
- https://renderman.pixar.com/forum/entitlement.php 사이트에서 트렉터를 위한 라이센스를 발급받는다.
- 서버에 두 파일을 복사한다.
- 라이센스파일을 /opt/pixar에 복사한다.
```
# cp ~/pixar.license /opt/pixar
```
- PixarLicense를 설치한다.
```
# rpm -i PixarLicense-LA-21.0_1634130-linuxRHEL6_gcc44icc150.x86_64.rpm
```

- 라이센스 서버를 설치하고 잘 실행되는지 확인한다.
```
# cd /opt/pixar/PixarLicense-LA-21.0
# sh linux_installService.sh
# PixarLicenseServer -x pixar.license
```

- 라이센스 서버를 설치하면 /etc/init.d/pixarlicenseserver 파일이 존재해서 서버가 실행될 때 자동으로 실행된다.
- 수동으로 서버상태를 변경할 때는 아래 명령어를 사용할 수 있다.
```
# /etc/init.d/pixarlicenseserver start
# /etc/init.d/pixarlicenseserver stop
# /etc/init.d/pixarlicenseserver restart
```

#### Tractor Engine 설치

#### Tractor Blade 설치
