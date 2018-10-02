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

## 환경변수 설정

- Tractor를 본격적으로 설치하기 앞서 먼저 환경변수를 설정해야한다.
- installation에는 json파일로 작성하여 /var/spool/tractor/에 config파일을 넣어라고 되어있다.
- 이 파일은 /opt/pixar/Tractor-x.x/config/안의 tractor.config파일을 복사하여 사용할 수 있다.

- /opt/pixar/Tractor-x.x/tractor.config를 열어 편집한다.
```
# vi tractor.config
```

- EngineOwner와 LicenseLocation을 수정해준다.
- EngineOwner가 설정되어 있지 않을시, tractor-engine 실행 오류가 발생한다. 꼭 설정해줄것!

```
 #"LicenseLocation":         "${TractorInstallDirectory}/../pixar.license",
 #"LicenseLocation":         "${PIXAR_LICENSE_FILE}",
 #"LicenseLocation":         "9010@hostname",
```

- 제일 아래에 있는 LicenseLocation의 hostname을 수정한다.
- Pixar.license의 위치가 다를경우 ${PIXAR_LICENSE_FILE}의 값을 해당 경로로 수정해준다.

```
# cp /opt/pixar/Tractor-x.x/config/tractor.config /var/spool/tractor
```

- 여기서 /var/spool/ 안의 tractor 폴더는 따로 생성해주어야한다.
- 경로를 전부 입력하여 mkdir를 실행하면 허가거부 오류를 일으킬때가 있어 경로로 이동 후에 만들어준다.
```
# cd /var/spool/
# mkdir tractor
```



### Tractor Engine 설치
- Engine 설치는 /opt/pixar/Tractor-x.x/에 설치된 파일들을 알맞은 위치로 이동시켜주고 실행시키는 작업을 한다.

1. sysVinit

- 첫번째 단계에서는 /opt/pixar/Tractor-x.x/lib/SystemServices/ 경로로 이동하여 작업한다.
- pixar setting file을 설치한다.
```
# cp pixar /etc/sysconfig
```

- tractor-engine을 설치한다.
```
# cp tractor-engine /etc/init.d
```

- 설치한 Engine을 시작시 바로 실행될 수 있게 활성화한다.
```
# chkconfig tractor-engine on
```

- 활성화된 Engine을 실행시켜준다.
- 파일명만 기재했을 경우 제대로 실행되지 않으니 반드시 경로를 입력해줄것!
```
# /etc/init.d/tractor-engine start
```

- 해제하는 방법은 stop을 입력해주면 된다.
```
# /etc/init.d/tractor-engine stop
```

- 아래 커맨드를 통해 tractor-engine이 실행되어있는 것을 확인할 수 있다.
```
# service --status-all|grep tractor
```

2. systemd
- 두번째 단계에서는 /opt/pixar/Tractor-x.x/lib/SystemServices/systemd/ 경로로 이동하여 작업한다.
- Engine Unit file을 설치한다.
```
# cp tractor-engine.service /usr/lib/systemd/system
```

- Engine setting file을 설치한다.
- 이 파일을 설치하기 앞서 /etc/sysconfig/로 경로를 이동한 후, tractor-engine 폴더를 만들어준다. (경로가 tractor-engine 폴더 안으로 지정되어 있기때문에 만들어주지않으면 실행 오류 발생.)
```
# cd /etc/sysconfig/
# mkdir tractor-engine
# cd /opt/pixar/Tractor-x.x/lib/SystemServices/systemd/

# cp tractor-engine /etc/sysconfig/tractor-engine
```

- systemd안의 Engine Service를 활성화한다.
```
# systemctl enable tractor-engine
```

- 활성화된 Engine을 실행한다. sysVinit과 마찬가지로 start와 stop으로 조작가능.
```
# systemctl start tractor-engine
# systemctl stop tractor-engine
```

- Engine 로그는 journald를 이용해 확인이 가능하다.
```
# journaldctl --since today -u tractor-engine
```

- 정상적인 메세지는 다음과 같다.
```
10월 02 15:31:09 mgskorea systemd[1]: Started Tractor Engine Service.
10월 02 15:31:09 mgskorea systemd[1]: Starting Tractor Engine Service...
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 TIMESTAMP 2018-10-02 pid=5425  (logging begins)
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 NOTICE Tractor 2.2 (Feb 15 2017 11:38:27 1715407 linux-ix86-64-gcc44icc150-relea
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 NOTICE tractor-engine start, pid=5425 mgskorea
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   application dir: /opt/pixar/Tractor-2.2
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   config file dir: /opt/pixar/Tractor-2.2/config
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   data dir:        /var/spool/tractor
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   dashboard root:  /opt/pixar/Tractor-2.2/lib/website
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   cmd-log dir:     /var/spool/tractor/cmd-logs
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   cmd log fetch: /tractor/cmd-logs/%u/J%j/T%t.log
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   license: /opt/pixar/Tractor-2.2/../pixar.license
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   tuning: iot=16  udi=50,5,4 stats=40,15,0  fdmax=500000
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   scheduling base: P+FIFO
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 DEBUG  preparing tcp listener on port 80
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 NOTICE listening on tcp port 80
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 DEBUG  preparing udp listener on port 80
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 NOTICE listening on udp port 80
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 NOTICE engine operating login: 'mgskorea'
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   engine discovery service, @:80
10월 02 15:31:09 mgskorea bash[5425]: 10/02 15:31:09 INFO   launching /opt/pixar/Tractor-2.2/bin/tractor-dbctl
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 NOTICE dbctl ok, rc=0 (dbpid=5507)  Initializing postgres database directory /va
10월 02 15:31:20 mgskorea bash[5425]: Starting postgres server on the data directory /var/spool/tractor/psql and on port 9876.
10월 02 15:31:20 mgskorea bash[5425]: Building tractor database.
10월 02 15:31:20 mgskorea bash[5425]: Upgrading database schema from version 2.2-4 to version 2.2-4.
10월 02 15:31:20 mgskorea bash[5425]: Applying upgrade 1 of 4: Drop all views.
10월 02 15:31:20 mgskorea bash[5425]: Applying upgrade 2 of 4: Drop all functions.
10월 02 15:31:20 mgskorea bash[5425]: Applying upgrade 3 of 4: Create all views.
10월 02 15:31:20 mgskorea bash[5425]: Applying upgrade 4 of 4: Create all functions.
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 DEBUG  database: host=localhost:9876 db=tractor
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 SEVERE code 10001:  gethostbyname Unknown host name
10월 02 15:31:20 mgskorea bash[5425]: license source: 9010@MGSKOREA
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 NOTICE launch cmd-log server: pid = 5554
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 INFO   loading /opt/pixar/Tractor-2.2/config/crews.config
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 DEBUG  valid logins: native system logins + 0 enumerated
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 DEBUG  password validator: (not enabled)
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 DEBUG  scanning prior user sessions
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 DEBUG  recently active user accounts found: 0
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 INFO   loading /opt/pixar/Tractor-2.2/config/blade.config
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 INFO   loading /opt/pixar/Tractor-2.2/config/limits.config
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 DEBUG  thread start: status-antenna
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 INFO   job queue rescan:  nvis=0 (0.002s)
10월 02 15:31:20 mgskorea bash[5425]: 10/02 15:31:20 INFO   job queue load:  runnable subset=0 (0.000s)
```


### Tractor Blade 설치
