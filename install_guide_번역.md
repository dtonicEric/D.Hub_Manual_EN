D.Hub패키지를 이용한_D\.Hub설치가이드__

__0\.5 Version__

1\. __  __설치__  __D\.Hub__  __ __  __패키징 설치 개요__

2\. __  __사전 준비__

__3\. __  __D\.Hub__  __ Packaging \(mini & original __  __공통__  __\) __  __설치__

__별첨__  __                 __  __ __  __        __  __ __

| 버전 | 내용 | 작성자 |
| :-: | :-: | :-: |
| 0.1 | 최초작성 | Mike, YJ |
| 0.2 | 사설 인증서 내용 삭제 | Jenna |
| 0.3 | docker log rolling 관련 옵션 추가 | Soo |
| 0.4 | 패키징 구성 변경으로 인한 가이드 수정 | Damon |
| 0.5 | 가이드 내용 보완 및 명령어 보완 | Eric |
|  |  |  |
|  |  |  |



<span style="color:#07689F">1</span>  <span style="color:#07689F">\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> 패키징 설치 가이드</span>

<span style="color:#40A8C4">1\.1 </span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">D\.Hub</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">패키징 설치</span>  <span style="color:#40A8C4">란</span>

* D\.Hub 패키징 설치는 D\.Hub 데이터 플랫폼의 모듈 구성을 쉽게 할 수 있도록 제공되는 설치 구성입니다\.
* D\.Hub 데이터 플랫폼의 모듈 구성은 Docker를 활용한 MSA\(Microservice Architecture\) 기반으로 이루어지며\, 하나의 서버에 일괄적으로 구성하는 형태가 기본입니다\.
* 패키징 설치는  __mini__ 와  __original__  두 가지로 구분됩니다\. __mini __  __패키지__ 는 Big Data Storage 구성이 포함되지 않으며\, __original __  __패키지__ 는 Big Data Storage 구성을 포함하고 있습니다\.
* 가이드에서 파일 경로는 압축 해제된 디렉토리를 기준으로 상대 경로로 작성되었습니다\.
* 본 설치 가이드는 root 권한을 기준으로 작성되었으며\, 모든 명령어는 root 권한으로 실행해야 합니다\.
* 패키지 가이드는 0\.4 버전 이후로 패키징 구성이 변경되었습니다\.
        * 기존에는 모든 D\.Hub 모듈의 Docker 이미지를 포함하는 방식이었으나\, 현재는 openjdk 이미지를 기반으로 빌드하는 방식으로 변경되었습니다\.
        * Kafka\, httpd\, postgis\, rabbitmq와 같은 3rd 파티 구성 요소는 별도의 파일로 제공되며\, 이들은 버전 변경이 거의 없다는 가정하에 구성됩니다\.



<span style="color:#40A8C4">2\.1 docker</span>  <span style="color:#40A8C4"> 및 </span>  <span style="color:#40A8C4">docker\-compose</span>

| 1) Docker 및 Docker compose설치 - centOS<br /><br />D.Hub 패키지는 Docker Compose 기반으로 구성되어 있으므로, Docker 엔진과 Docker Compose를 설치해야 합니다.<br /><br />D.Hub 패키지는 Docker Compose 파일 버전 3.5를 사용하고 있으며, <br />이에 따라 Docker 엔진 17.12.0 버전 이상을 권장합니다.<br /><br />Docker 엔진 권장 버전은 25.0.1 입니다.<br /><br />만약 Docker와 Docker Compose가 이전에 설치되지 않았다면, 아래 명령어를 사용하여 설치할 수 있습니다.<br /> |
| :-: |
| # yum 패키지 업데이트<br />$ sudo yum –y update<br /># 패키지 매니저 관련 유틸 업데이트<br />$ sudo yum install -y yum-utils<br /><br /><br /># docker repository 추가<br />$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo<br /># docker 및 docker compose 설치<br />$ sudo yum install docker-ce-25.0.1 docker-ce-cli-25.0.1 containerd.io docker-compose-plugin  -y<br /># docker 실행<br />$ systemctl start docker<br /># docker 데몬등록<br />$ systemctl enable docker<br /><br /><br /># docker version 확인<br />$ docker –version<br /># docker compose version 확인<br />$ docker compose version |

<span style="color:#FF0000"> _※1 _ </span>  <span style="color:#FF0000"> _참고_ </span>  <span style="color:#FF0000"> _: _ </span>  <span style="color:#FF0000">https://marklee1117\.tistory\.com/m/84</span>

<span style="color:#FF0000"> _※2 _ </span>  <span style="color:#FF0000"> _상기 내용은 _ </span>  <span style="color:#FF0000"> _Centos _ </span>  <span style="color:#FF0000"> _기준으로 설치하는 것을 염두에 두고 _ </span>  <span style="color:#FF0000"> _yum_ </span>  <span style="color:#FF0000"> _을 이용하여 설치하므로_ </span>  <span style="color:#FF0000"> _\, ubuntu _ </span>  <span style="color:#FF0000"> _등을 이용할 때는 _ </span>  <span style="color:#FF0000"> _apt\-get _ </span>  <span style="color:#FF0000"> _등을 이용하여 설치_ </span>  <span style="color:#FF0000"> _\(_ </span>  <span style="color:#FF0000"> _참고_ </span>  <span style="color:#FF0000"> _: _ </span>  <span style="color:#FF0000"> _[Docker BuildKit \- Overview \(azure\.com\)](https://dev.azure.com/dtonic/AI-LAB/_wiki/wikis/AI-LAB.wiki/5556/Docker-BuildKit)_ </span>  <span style="color:#FF0000"> _\)_ </span>



<span style="color:#40A8C4">2\.1 docker</span>  <span style="color:#40A8C4"> 및 </span>  <span style="color:#40A8C4">docker\-compose</span>

| 1) Docker 및 Docker compose설치 - ubuntu<br /><br />일반적으로 아래에 적힌 명령어들을 사용하여 설치할 수 있습니다.<br /><br />Ubuntu에 Docker Engine 설치 매뉴얼<br />폐쇄망의 경우, 하이퍼링크를 참고하여 직접 다운로드하여 설치하는 방법을 사용할 수 있습니다. |
| :-: |
| # Add Docker's official GPG key:<br />$ sudo apt-get update<br />$ sudo apt-get install ca-certificates curl<br />$ sudo install -m 0755 -d /etc/apt/keyrings<br />$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc<br />$ sudo chmod a+r /etc/apt/keyrings/docker.asc<br /><br /># Add the repository to Apt sources:<br />$ echo \<br />  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \<br />  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \<br />  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null<br /><br />$ sudo apt-get update<br /><br /># List the available versions:<br />$ apt-cache madison docker-ce | awk '{ print $3 }'<br /><br />5:26.1.0-1~ubuntu.24.04~noble<br />5:26.0.2-1~ubuntu.24.04~noble<br />...<br /><br /># To install a specific version<br />$ VERSION_STRING=5:26.0.0-1~ubuntu.24.04~noble<br />$ sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin=2.27.0-1~ubuntu.24.04~noble<br /> |


<span style="color:#40A8C4">2\.1 docker</span>  <span style="color:#40A8C4"> 및 </span>  <span style="color:#40A8C4">docker\-compose</span>

| 1) Docker 및 Docker compose설치 - ubuntu |
| :-: |
| # docker 데몬등록<br />$ sudo systemctl enable docker<br /># docker 실행<br />$ sudo systemctl start docker<br /><br /># docker version 확인<br />$ docker --version<br /># docker compose version 확인<br />$ docker compose version |



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

mini 및 original 패키징 Zip 파일은 [https://nexus\.dtonic\.io](https://nexus.dtonic.io/)의 하기 경로에 업로드 됩니다\.

_업로드 경로_  : Browse > raw > product > DHub

다운받고자 하는 D\.Hub 버전의 mini / original의 zip 파일을 선택 후 우측에 표시된 Path 링크를 마우스 우 클릭 하여\, “링크 주소 복사＂를 하고\, 하기 명령을 통해 설치하고자 하는 서버에 다운을 받습니다\.

| 1) mini 패키징 Zip 파일 다운받기 (예시) mini 1.1.3-b version 다운로드  |
| :-: |
| $ cd /home/ubuntu<br />$ wget https://nexus.dtonic.io:8443/repository/raw/product/DHub/dhub-mini/1.1.3-b/dhub-mini-1.1.3-b.zip |



상기와 같은 방법으로 Original 패키징 Zip도 하기 명령을 통해 설치하고자 하는 서버에 다운을 받습니다\.

| 2) Original 패키징 Zip 파일 다운받기 (예시) original 1.1.3 version 다운로드 |
| :-: |
| $ cd /home/ubuntu<br />$ wget https://nexus.dtonic.io/repository/raw/product/DHub/dhub-original/1.1.3/dhub-original-1.1.3.zip |


<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.2</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">Zip</span>  <span style="color:#40A8C4"> 파일 압축 해제 및 세부 설정 수정 </span>

| Zip 파일 압축 해제 |
| :-: |
| $ sudo apt-get install unzip<br />$ cd /home/ubuntu<br />$ unzip dhub-{mini/original}-{version}.zip |

unzip dhub\-\{mini/original\}\-\{version\}\.zip 이후 출력에 A 를 눌러 모든 파일을 압축 해제합니다\.

압축 해제된 디렉토리의 구조는 아래와 같습니다\.

_dhub_  _\-\{mini/original\}\-\{version\}\.zip_

_docker\-compose_    :   docker build에 필요한 설정 및 docker compose yml 파일을 포함하는 디렉토리

_docker\-images_       :   docker image 전체를 포함하는 디렉토리


<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.2</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">Zip</span>  <span style="color:#40A8C4"> 파일 압축 해제 및 세부 설정 수정 </span>

openjdk\, kafka\, httpd\, postgis\, rabbitmq 패키징 Zip 파일은 [https://nexus\.dtonic\.io:8443/](https://nexus.dtonic.io:8443/) 의 하기 경로에 업로드 됩니다\.

_업로드 경로_  : Browse > raw > product > DHub > dhub\-docker\-base

다운받고자 하는 dhub\-docker\-base 버전의 zip 파일을 선택 후 우측에 표시된 Path 링크를 마우스 우 클릭 하여\, “링크 주소 복사＂를 하고\, 하기 명령을 통해 dhub 설치 경로에 다운을 받습니다\.


| 2) dhub-docker-base Zip 파일 다운받기 (예시) dhub-docker-base 1.0.0 version 다운로드  |
| :-: |
| $ cd /home/ubuntu/dhub-{mini/original}-{version}/docker-images<br />$ wget https://nexus.dtonic.io:8443/repository/raw/product/DHub/dhub-docker-base/1.0.0/dhub-base-docker-images-1.0.0.zip |



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.2</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">Zip</span>  <span style="color:#40A8C4"> 파일 압축 해제 및 세부 설정 수정 </span>

| 3) Zip 파일 압축 해제 |
| :-: |
| $ cd /home/ubuntu/dhub-{mini/original}-{version}/docker-images<br />$ unzip dhub-base-docker-images-1.0.0.zip |

| 4) Docker build를 위한 이미지 로드 |
| :-: |
| $ cd /home/ubuntu/dhub-{mini/original}-{version}/docker-images<br />$ sudo ./load-docker-image.sh |

| 5) Docker build를 위한 설정 파일 수정 |
| :-: |
| Docker build를 위한 설정파일은 docker-compose 디렉토리 하위에 있는 하기 파일들을 수정합니다.<br /><br />자세한 수정 내용에 대해서는 다음 페이지를 참고해주시기 바랍니다.<br /><br />(1) application-common.yml<br /><br />(2) security/smartcity_back/server_conf.json |



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.3</span>  <span style="color:#40A8C4"> 세부 설정 수정 \- </span>  <span style="color:#40A8C4">PostgreSQL</span>  <span style="color:#40A8C4"> 비밀번호 변경 \(권장\)</span>

D\.Hub 패키지 최초 설치 시 PostgreSQL 데이터베이스의 기본 비밀번호는 \`dhub123\!\` 으로 설정되어 있습니다\.

보안 상의 이유로\, 사용자는 해당 비밀번호를 원하는 비밀번호로 변경하는 것을 권장합니다\.

| docker-compose.yml 파일 수정<br /><br />먼저 Vim 텍스트 에디터를 이용해서 docker-compose 경로의 docker-compose.yml 파일 수정합니다. |
| :-: |
| $ vi docker-compose/docker-compose.yml |

Docker\-compose\.yml 파일의 하기 99\, 100 Line의 사용자 계정 정보를 수정하면 됩니다\.



__\[__  __첨부__  __1\]__  __ __  __docker\-__  __compose\.yml__  __ __  __내 __  __postgis__  __ service __  __내 환경변수 수정 __  __\(DB__  __ 계정 정보 수정__  __\)__

_※_  _ docker\-_  _compose\.yml_  _ _  _파일을 수정한 후에는_  _\, \`_  _application\-common\.yml_  _\`_  _ 및 _  _\`_  _server\_conf\.json_  _\`_  _ 설정 파일에서 _  _PostgreSQL에_  _ 대한 접속 정보를 수정해야 합니다_  _\. _  _해당 내용은 뒷장에서 확인 가능합니다_  _\._



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.3</span>  <span style="color:#40A8C4"> 세부 설정 수정 \- </span>  <span style="color:#40A8C4">PostgreSQL</span>  <span style="color:#40A8C4"> 비밀번호 변경 \(권장\)</span>

| 2) application-common.yml 파일 수정 |
| :-: |
| $ vi docker-compose/application-common.yml |

| application-common.yml 파일에서 아래 내용을 찾아 새로 변경한 비밀번호로 변경해줍니다. |
| :-: |
| datasource:<br />  driverClassName: org.postgresql.Driver<br />  url: jdbc:postgresql://postgis4:5432/smart_city<br />  username: postgres<br />  password: dhub123!<br /><br />spring:<br />  security:<br />    enabled: false<br />  datasource:<br />    driverClassName: org.postgresql.Driver<br />    url: jdbc:postgresql://postgis4:5432/apigw?currentSchema=apigw # apigw 접속정보<br />    username: postgres<br />    password: dhub123! |

| 3) server_conf.json 파일 수정 |
| :-: |
| $ vi dhub-{mini/original}-{version}/docker-compose/security/smartcity_back/server_conf.json |

| application-common.yml 파일에서 아래 내용을 찾아 새로 변경한 비밀번호로 변경해줍니다. |
| :-: |
| “dbUserId” : “postgres”,<br />“dbUserPwd” : “dhub123!” |



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.3</span>  <span style="color:#40A8C4"> 세부 설정 수정 \- </span>  <span style="color:#40A8C4">PostgreSQL</span>  <span style="color:#40A8C4"> 비밀번호 변경 \(권장\)</span>

| 3) server_conf.json 파일 수정 |
| :-: |
| $ vi dhub-{mini/original}-{version}/docker-compose/security/smartcity_back/server_conf.json |



__\[__  __첨부__  __1\]__  Security module \(Backend\) server\_conf\.json

| Security module 내 PostgreSQL  DB 설정 변경 (dbUserId 및 dbUserPwd 변경) |
| :-: |
| “dbUserId” : “postgres”,<br />“dbUserPwd” : “dhub123!” |



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.3</span>  <span style="color:#40A8C4"> 세부 설정 수정 \- </span>  <span style="color:#40A8C4">포트 정보 변경 </span>  <span style="color:#40A8C4">\(</span>  <span style="color:#40A8C4">옵션</span>  <span style="color:#40A8C4">\)</span>

기본 포트 확인

D\.Hub가 기본적으로 사용하는 포트 정보는 아래 표와 같음

| 구분 | 모듈 이름 | 포트 이름 | 포트 값 (default) | 포트 설명 |
| :-: | :-: | :-: | :-: | :-: |
| dhub 모듈 | datacore-manager | DATACORE_MANAGER_PORT | 8080 | 　 |
|  | datacore-ingestinterface | DATACORE_INGEST_PORT | 8081 | 　 |
|  |  | DATACORE_INGEST_HTTPS_PORT | 443 | 　 |
|  | datacore-servicebroker | DATACORE_SERVICEBROKER_PORT | 8082 | 　 |
|  | datacore-ui | DATACORE_UI_PORT | 8083 | 　 |
|  | datacore-dashboard | DATACORE_DASHBOARD_PORT | 8084 | 　 |
|  | apigw | APIGW_SERVICE_PORT | 8085 | 서비스 포트 |
|  |  | APIGW_MANAGE_PORT | 8086 | 관리자 포트 (서비스 등록, 라우팅 룰 등록 등) |
|  | ingest-manager | INGEST_MANAGER_PORT | 8099 | 　 |
|  | security-module-front | SECURITY_MODULE_FRONT_PORT | 30001 | 　 |
|  | security-module-back | SECURITY_MODULE_BACK_PORT | 30000 | 　 |
| 3rd Party 모듈 | nifi | NIFI_PORT | 8443 | 　 |
|  |  | NIFI_LISTEN_HTTP_PORT | 20000-20099 | 　 |
|  | apache2 | APACHE2_PORT | 38081 | 　 |
|  | zookeeper | KAFKA_ZOOKEEPER_PORT | 2181 | 　 |
|  | broker | KAFKA_BROKER_PORT | 9092 | 　 |
|  |  | KAFKA_CONTROLLER_PORT | 29092 | 　 |
|  | rest-proxy | KAFKA_REST_PORT | 18082 | 　 |
|  | rabbitmq | RABBITMQ_MESSAGE_BROKER_PORT | 5672 | 　 |
|  |  | RABBITMQ_UI_PORT | 15672 | 　 |
|  |  | RABBITMQ_MQTT_PORT | 1883 | 　 |
|  | postgis4 | POSTGRESQL_PORT | 5432 | PostgreSQL 데이터베이스 포트 |
| 빅데이터 모듈 | thrift | BIGDATA_SPARK_THRIFT_PORT | 10000 | Spark Thrift Server 포트 |
|  | 　 | BIGDATA_SPARK_UI_PORT | 4040 | Spark UI 포트 |
|  | 　 | BIGDATA_ZOOKEEPER_PORT | 2189 | HBase가 사용하는 Zookeeper 포트 |

| 2) 포트 설정 변경<br /><br />위의 기본 포트 설정을 변경하고자 하는 경우, docker-compose 경로의 .env 파일 수정을 통해 변경 가능 |
| :-: |
| $ vi docker-compose/.env |



__\[__  __첨부__  __??\]__  __ 예__  __\) \.env__  __ 파일을 통한 __  __Datacore Manager __  __포트 정보 변경 예시__



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.3</span>  <span style="color:#40A8C4"> 세부 설정 수정 </span>  <span style="color:#40A8C4">– </span>  <span style="color:#40A8C4">기타 설정 변경 </span>  <span style="color:#40A8C4">\(</span>  <span style="color:#40A8C4">옵션</span>  <span style="color:#40A8C4">\)</span>  <span style="color:#40A8C4"> </span>

<span style="color:#FF0000"> _※ _ </span>  <span style="color:#FF0000"> _암호화 알고리즘의 변경의 경우 _ </span>  <span style="color:#FF0000"> _1_ </span>  <span style="color:#FF0000"> _차 패키징에서는 미지원 _ </span>  <span style="color:#FF0000"> _\-> _ </span>  <span style="color:#FF0000"> _추후 제공 예정_ </span>

_※_  _ _  _D\.Hub_  _ _  _module_  _의 _  _HTTP/HTTPS _  _설정을 변경하는 경우에는 _  _“_  _별첨 _  _1\._  _ _  _HTTPS / SSL_  _ 인증서 설정_  __ __ 부분을 참고해주시기 바랍니다\.

_※_  _ _  _D\.Hub_  _ _  _module_  _의 로그 설정을 변경하는 경우에는 _  _“_  _별첨 _  _3\._  _ 로그 레벨 설정 변경_  _”_  _의 _  _1\)\, 2\)_  _번 항목을 참고해주시기 바랍니다_  _\. _

<span style="color:#40A8C4">3\.4 </span>  <span style="color:#40A8C4">D\.Hub</span>  <span style="color:#40A8C4"> module </span>  <span style="color:#40A8C4">도커</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">컨테이너 실행</span>

| 1) start-dhub.sh 파일 실행 |
| :-: |
| $ cd /home/ubuntu<br />$ sudo dhub-{mini/original}-{version}/docker-compose/start-dhub.sh |

_※ start\-dhub\.sh_  _ 파일의 경우_  _\, _  _초기 _  _D\.Hub_  _ module _  _도커_  _ 컨테이너 _  _실행시_  _ 사용되는 스크립트 파일입니다_  _\._



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

<span style="color:#40A8C4">3\.3</span>  <span style="color:#40A8C4"> 세부 설정 수정 </span>  <span style="color:#40A8C4">– </span>  <span style="color:#40A8C4">기타 설정 변경 </span>  <span style="color:#40A8C4">\(</span>  <span style="color:#40A8C4">옵션</span>  <span style="color:#40A8C4">\)</span>  <span style="color:#40A8C4"> </span>

<span style="color:#FF0000"> _※ _ </span>  <span style="color:#FF0000"> _암호화 알고리즘의 변경의 경우 _ </span>  <span style="color:#FF0000"> _1_ </span>  <span style="color:#FF0000"> _차 패키징에서는 미지원 _ </span>  <span style="color:#FF0000"> _\-> _ </span>  <span style="color:#FF0000"> _추후 제공 예정_ </span>

_※_  _ _  _암호화 알고리즘의 경우 _  _Default_  _로 _  _Bcrypt_  _ _  _방식이 적용되어 있음_

_※_  _ _  _암호화 알고리즘은 _  _Security_  _ _  _module_  _에서 _  _Bcrypt_  _ _  _및 _  _SHA\-256_  _ 방식을 제공하고 있으며_  _\, _  _Bcrypt_  _ _  _방식으로 설정하는 경우에는 _  _“_  _encryptionAlgorithm_  _”_  _의 값을 _  _“_  _bcrypt_  _”_  _로_  _\, SHA\-256_  _ 방식으로 설정을 하는 경우에는 _  _“sha”_  _로 값을 설정해야 합니다_  _\. \(_  _공공기관에 납품하는 경우에는 보안 감리를 위해 _  _“sha”_  _로 설정_  _\)_

| 1) server_conf.json 파일 수정 (파일의 경로는 압축 해제한 디렉토리를 기준으로 상대경로로 작성) |
| :-: |
| $ vi dhub-{mini/original}-{version}/docker-compose/security/smartcity_back/server_conf.json |



__\[__  __첨부__  __1\]__  Security module \(Backend\) server\_conf\.json

| 2)   Security module 내 암호화 알고리즘 설정 변경 (encryptionAlgorithm 변경) |
| :-: |
| “encryptionAlgorithm” : “bcrypt” |



<span style="color:#07689F">3\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> Packaging \(mini & original </span>  <span style="color:#07689F">공통</span>  <span style="color:#07689F">\) </span>  <span style="color:#07689F">설치</span>

| 1) 계정 정보<br /><br />기본값으로 설치한 D.Hub의 접속 정보는 다음과 같습니다. |
| :-: |
| # PostgreSQL<br />- ID: postgres / PW: dhub123!<br />- 데이터베이스<br />   - smart_city: datacore, ingest-manager서비스가 사용<br />   - apigw: apigw 서비스가 사용<br />   - security: security-module이 사용<br />   - hive: hive의 메타데이터 관리 데이터베이스로 사용 (original 패키지 only)<br /><br /># D.Hub 계정<br />- System Admin - ID: admin / PW: admin45@<br />- Dhub Admin - ID: dhub / PW: dhub45@ |

| 2) UI 접속정보<br /><br />D.Hub에서 제공하는 UI는 다음과 같습니다 |
| :-: |
| - datacore-ui: http://<ip>:8083<br />- ingest-manager: http://<ip>:8099<br />- datacore-dashboard: http://<ip>:8084<br />- security-module-front: http://<ip>:30000 |

| 3) Single Sign-On 설정<br /><br />D.Hub 하위 모듈들에 대한 Single Sign-On 설정의 경우 기본으로 활성화되어 있습니다.<br /><br />기본적으로 아래 모듈들에 대해서 어플리케이션 등록이 되어 있습니다. |
| :-: |
| - datacore-ui<br />- ingest-manager<br />- datacore-dashboard |


__별첨 __  __1\. HTTPS / SSL__  __ 인증서 설정__

__별첨 __  __2\. D\.Hub__  __ __  __module __  __포트 변경__

__별첨 __  __3__  __\. __  __로그 레벨 설정 __  __변경__

__별첨 __  __4\. Docker log rolling__  __ 설정__  __                         __


<span style="color:#07689F">별첨 </span>  <span style="color:#07689F">1</span>  <span style="color:#07689F">\. </span>  <span style="color:#07689F">HTTPS</span>  <span style="color:#07689F"> </span>  <span style="color:#07689F">/ SSL </span>  <span style="color:#07689F">인증서 설정</span>

<span style="color:#40A8C4">1\.1</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">폐쇄망에서 </span>  <span style="color:#40A8C4">Private Certificate Authority \(CA\) </span>  <span style="color:#40A8C4">생성 및 관리</span>

D\.Hub를 https로 사용하고 싶은 경우 공인 인증 된 인증서가 필요함

| 1) SSL 인증서 파일을 위치하기 위한 디렉토리 생성 및 인증서 파일 복사 |
| :-: |
| <br /># ssl 관련 인증 파일을 위치하기 위한 디렉토리 생성<br />$ mkdir /etc/httpd/ssl<br /><br /># 사용할 인증서를 Apache docker 컨테이너 내 mount directory로 이동<br />$ cp /etc/httpd/ssl/server.crt ${패키지 설치 경로}/docker-compose/ssl/server.crt<br />$ cp /etc/httpd/ssl/server.key ${패키지 설치 경로}/docker-compose/ssl/server.key<br />$ cp /etc/httpd/ssl/ca.crt ${패키지 설치 경로}/docker-compose/ssl/ca.crt |

| 2) httpd.conf.sh (httpd.conf 파일 내에 SSL 인증과 관련된 Script 추가) |
| :-: |
| # httpd.conf.sh 파일 실행<br />$ ./httpd.conf.sh {datacore ui domain 주소} {ingestmanager domain 주소} {security frontend domain 주소} {security backend domain 주소} |


<span style="color:#07689F">별첨 </span>  <span style="color:#07689F">1</span>  <span style="color:#07689F">\. </span>  <span style="color:#07689F">HTTPS</span>  <span style="color:#07689F"> </span>  <span style="color:#07689F">/ SSL </span>  <span style="color:#07689F">인증서 설정</span>

<span style="color:#40A8C4">1\.1</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">폐쇄망에서 </span>  <span style="color:#40A8C4">Private Certificate Authority \(CA\) </span>  <span style="color:#40A8C4">생성 및 관리</span>

| 3) Hosts 파일 수정을 위한 Windows 스크립트 파일 실행 (modify_hosts.ps1) |
| :-: |
| <br /># Windows OS 내에 modify_hosts.ps1 파일 복사<br /><br /># Windows PowerShell 실행 후 복사한 modify_hosts.ps1 실행 (Datacore UI, Ingestmanager, Security FE/BE에 대해 모두 추가)<br />> .\modify_hosts.ps1 -ipAddress “{IP 주소}" -domain “{도메인 주소}" |

| 4) HTTPS 접속 확인 (HTTPS의 경우에는 할당한 도메인 주소를 사용해서 접속 확인) |
| :-: |
|  |



| 5) HTTP 접속 확인 (HTTP의 경우에는 IP로 접속해서 확인) |
| :-: |
|  |



<span style="color:#07689F">별첨 </span>  <span style="color:#07689F">1</span>  <span style="color:#07689F">\. </span>  <span style="color:#07689F">HTTPS</span>  <span style="color:#07689F"> </span>  <span style="color:#07689F">/ SSL </span>  <span style="color:#07689F">인증서 설정</span>

<span style="color:#40A8C4">1\.1</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">폐쇄망에서 </span>  <span style="color:#40A8C4">Private Certificate Authority \(CA\) </span>  <span style="color:#40A8C4">생성 및 관리</span>

| 6) context 파일 서버 접속 확인 (38081 포트로 확인) |
| :-: |
|  |


<span style="color:#07689F">별첨 </span>  <span style="color:#07689F">2</span>  <span style="color:#07689F">\. </span>  <span style="color:#07689F">D\.Hub</span>  <span style="color:#07689F"> module </span>  <span style="color:#07689F">포트 </span>  <span style="color:#07689F">변경</span>  <span style="color:#07689F"> </span>

_※_  _ _  _D\.Hub_  _ 서비스의 경우 _  _docker\-compose_  _로 구성되어 있어_  _\, _  _도커_  _ 컨테이너 내부와 호스트 서버 간에 _  _포트포워딩이_  _ 이루어져 있음_  _\._

_접속 주소 변경을 원하는 경우_  _\, _  _호스트로의 _  _포트포워딩_  _ 정보만 간단하게 수정 가능_

| 1) 3.3 세부 설정 수정 - 포트 정보 변경 (옵션) 내용 사전 진행<br /><br /><br />2) 추가 설정 (Datacore UI, Dashboard, Ingest Manager 포트 변경의 경우에만 진행 !)<br />아래 명령어 실행을 통해 Security Module에 등록된 어플리케이션 정보 수정<br /> |
| :-: |
| $ sh docker-compose/security/register-application.sh |

| 3) 해당 모듈 재시작<br /><br />아래 명령어를 통해 해당 모듈을 완전히 삭제한 뒤, 재시작해야 함 |
| :-: |
| $ docker stop <모듈 이름><br /><br />$ docker rm <모듈 이름><br /><br />$ docker-compose up –d --no-recreate <모듈 이름> |

| 4) 추가 설정 (Security Module Backend의 포트 변경의 경우에만 진행 !)<br />아래 명령어 실행을 통해 Security Module에 등록된 어플리케이션 정보 수정<br /> |
| :-: |
| $ sh docker-compose/security/register-application.sh |


<span style="color:#07689F">별첨 3</span>  <span style="color:#07689F">\.</span>  <span style="color:#07689F"> 로그 레벨 설정 변경</span>

<span style="color:#40A8C4">3\.1 </span>  <span style="color:#40A8C4">D\.Hub</span>  <span style="color:#40A8C4"> 하위 각 </span>  <span style="color:#40A8C4">모듈별</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">logback</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">파일 수정</span>

| 1) Nexus 서버로부터 다운받은 *.zip 파일 내 하기 경로에 있는 logback-spring-*.xml 파일을 수정 |
| :-: |
| <br />※ D.Hub 하위 모듈명 : datacore-ui, datacore-dashboard, datacore-pushagent, datacore-manager, datacore-ingestinterface, datacore-servicebroker, ingest-manager, apigw<br /><br /># Datacore 하위 모듈의 경우, <br />Dhub-{mini/original}-{version}/docker-compose/datacore/{D.Hub 하위 모듈명}/logback-spring-rel.xml <br /><br /># IngestManager 모듈의 경우, <br />Dhub-{mini/original}-{version}/docker-compose/ingest-manager/logback-spring-rel.xml |

| 2) Log Level 수정<br />상기 로그 환경 설정파일(logback-spring-*.xml)에서 다양한 logger들의 로그 레벨 및 패키지 정보를 설정할 수 있습니다. |
| :-: |
| # logback-spring-*.xml 파일 내 구조<br /><br /><configuration><br /><br />    <!--  로그레벨을 정의할  구체적인 패키지 및 클래스 정보와 로그레벨을 정의 --><br />    <logger name="com.example.package" level="DEBUG" /><br />    <logger name="org.springframework" level="INFO" /><br /><br />    <!– 최상위 루트 로그 레벨 정의 --><br />    <root level="INFO"><br />        <!– appender-ref가 정의되어 있는 경우, Log 메시지를 정의된 appender로 forwarding --><br />       <!– 아래 예시의 경우, 유입된 Log 메시지를 ref에 정의된 console에 출력하도록 한다.--><br />        <appender-ref ref="CONSOLE" /><br />    </root><br /><br /></configuration><br /> |

_※ \<logger>_  _ 요소의 경우_  _\, log_  _ _  _level_  _ 및 _  _log_  _ _  _level_  _을 정의할 구체적인 패키지 및 클래스 정보를 정의합니다_  _\. _

_※ \<root>_  _ 요소의 경우_  _\, _  _모든 패키지들에 적용할 _  _root log_  _ _  _level_  _에_  _ _  _대한 정의합니다_  _\._

_※_  _ 적용 가능한 로그 레벨의 정보는 다음과 같습니다_  _\. => TRACE\, DEBUG\, INFO\, WARN\, ERROR_

| 3) logback-spring-*.xml 파일 수정을 완료하면, 아래 명령어로 해당 모듈을 재시작합니다. |
| :-: |
| $ docker restart <모듈 이름> |

<span style="color:#07689F">별첨 </span>  <span style="color:#07689F">4</span>  <span style="color:#07689F">\.</span>  <span style="color:#07689F"> </span>  <span style="color:#07689F">Docker log rolling </span>  <span style="color:#07689F">설정</span>

<span style="color:#40A8C4">4\.1 </span>  <span style="color:#40A8C4">D\.Hub</span>  <span style="color:#40A8C4"> 하위 각 </span>  <span style="color:#40A8C4">모듈별</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">log rolling </span>  <span style="color:#40A8C4">설정</span>

_※_  _ _  _D\.Hub_  _ 서비스의 경우 하위 개별 모듈의 _  _logback\-spring _  _등을 통해 _  _log_  _가 무한정 쌓이는 것을 방지하는 _  _log rollingPolicy _  _옵션이 설정되어 있음_  _\. _  _그러나 _  _docker log_  _의 경우 컨테이너 내부에 쌓이는 것이 아닌 서버에 쌓이는 로그로 이를 위한 별도의 _  _log rolling _  _설정이 필요함_  _\._

_v1\.1\.6 _  _이후의 패키지에는 기본 설정으로 _  <span style="color:#FF0000"> _각 모듈별 로그에 대한 _ </span>  <span style="color:#FF0000"> _rolling _ </span>  <span style="color:#FF0000"> _설정이 _ </span>  <span style="color:#FF0000"> _max\-file 30\, max\-size 200m_ </span>  <span style="color:#FF0000"> _으로 개별 지정되어 있음_ </span>  _\(_  _이 설정은 당시 _  _logback\-spring rollingPolicy _  _설정에 근거함_

| max-file 및 max-size 설정 변경Nexus 서버로부터 다운받은 *.zip 파일 내 docker-compose-{패키지명}.yml 내 services 아래 개별 모듈 항목에 작성되어 있는 설정 변경(참고: JSON File logging driver | Docker Docs) |
| :-: |
| services:<br />  apache2:<br />    image: httpd:${APACHE2_VERSION}<br />    container_name: apache2<br />    ports:<br />      - ${APACHE2_PORT}:80<br />    logging:<br />      driver: "json-file"<br />      options:<br />        max-file: "30"<br />        max-size: "200m"<br />  zookeeper:<br />    image: confluentinc/cp-zookeeper:${KAFKA_CONFLUENT_VERSION}<br />    tmpfs: /tmp<br />    hostname: zookeeper<br />    container_name: zookeeper<br />    ports:<br />      - ${KAFKA_ZOOKEEPER_PORT}:2181<br />    logging:<br />      driver: "json-file"<br />      options:<br />        max-file: "30"<br />        max-size: "200m“<br />… |

<span style="color:#07689F">별첨 </span>  <span style="color:#07689F">4</span>  <span style="color:#07689F">\.</span>  <span style="color:#07689F"> </span>  <span style="color:#07689F">Docker log rolling </span>  <span style="color:#07689F">설정</span>

<span style="color:#40A8C4">4\.1 </span>  <span style="color:#40A8C4">D\.Hub</span>  <span style="color:#40A8C4"> 하위 각 </span>  <span style="color:#40A8C4">모듈별</span>  <span style="color:#40A8C4"> </span>  <span style="color:#40A8C4">log rolling </span>  <span style="color:#40A8C4">설정</span>

_※_  _ _  _D\.Hub_  _ 서비스의 경우 하위 개별 모듈의 _  _logback\-spring _  _등을 통해 _  _log_  _가 무한정 쌓이는 것을 방지하는 _  _log rollingPolicy _  _옵션이 설정되어 있음_  _\. _  _그러나 _  _docker log_  _의 경우 컨테이너 내부에 쌓이는 것이 아닌 서버에 쌓이는 로그로 이를 위한 별도의 _  _log rolling _  _설정이 필요함_  _\._

_v1\.1\.6 _  _이후의 패키지에는 기본 설정으로 _  <span style="color:#FF0000"> _각 모듈별 로그에 대한 _ </span>  <span style="color:#FF0000"> _rolling _ </span>  <span style="color:#FF0000"> _설정이 _ </span>  <span style="color:#FF0000"> _max\-file 30\, max\-size 200m_ </span>  <span style="color:#FF0000"> _으로 개별 지정되어 있음_ </span>  _\(_  _이 설정은 당시 _  _logback\-spring rollingPolicy _  _설정에 근거함_

| 2) 전체 모듈에 동일한 설정 적용<br />모듈별로 설정을 분리하지 않고 동일한 설정을 사용하는 경우 다음의 방법을 사용할 수 있음 |
| :-: |
| # /etc/docker/daemon.json<br /><br />{<br />        "log-driver": "json-file",<br />        "log-opts": {<br />                "max-size" : "10k",<br />                "max-file" : "5"<br />        }<br />} |
| /etc/docker/daemon.json에 설정한 내용은 docker container 전체에 적용되는 기본값임. 작성 이후 docker service를 재시작해줘야 함. daemon.json 작성 후 생성된 컨테이너에 대해서만 적용됨(작성 이전 생성된 컨테이너에는 적용되지 않음) |
