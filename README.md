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

- 서버가 늘 실행될 때 마다 자동으로 실행되기 위해 init.d 설정을 한다.
```
```
