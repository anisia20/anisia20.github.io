# How to build project
### Maven behind Proxy
If you need to execute build behind the proxy, edit the *.mvn/jvm.config* and put the follow properties:
```properties
-Dhttp.proxyHost=proxy_ip
-Dhttp.proxyPort=proxy_port
-Dhttps.proxyHost=proxy_ip
-Dhttps.proxyPort=proxy_port
-Dhttp.proxyUser=username
-Dhttp.proxyPassword=password
```
### Build from GitHub
git clone https://github.com/apache/skywalking.git
cd skywalking/
git submodule init
git submodule update
./mvnw clean package -DskipTests #jdk 오류시 java_home 잡아줄것

#### Compile agent and package
vi Makefile
SKIP_TEST?=true
1안)
./make build.agent
2안)
./mvnw package -Pagent,dist
# cd plugin_module_dir & mvn clean package
#### Compile backend and package
1안)
./make build.backend
2안)
./mvnw package -Pbackend,dist
####  Compile UI and package
1안)
./make build.ui
2안)
./mvnw package -Pui,dist
####
./mvnw compile -Dmaven.test.skip=true
## Quickstart
make docker
docker image ls | grep skywalking
export HUB=foo.io && export TAG=bar && export ES_VERSION=es7 && make docker
docker image ls | grep foo.io
cd docker
vi .env
TAG=latest
ES_TAG=6.8.1
sudo su -
docker-compose up
GitHubGitHub
apache/skywalking
APM, Application Performance Monitoring System. Contribute to apache/skywalking development by creating an account on GitHub.


