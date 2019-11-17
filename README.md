# Setup MYSQL
Install MYSQL via helm chart
##### check if stable repository is already added
* helm repo list
##### add stable repository if not yet present in the list
* helm repo add stable https://kubernetes-charts.storage.googleapis.com/
##### install stable/mysql chart
* helm search mysql
* helm install stable/mysql --name mtap-db
##### get base64 password and decode it (https://www.base64decode.org/)
* kubectl get secret mtap-db -o jsonpath="{.data.mysql-root-password}"
##### enable port forwarding to access outside k8s cluster
* kubectl port-forward mtap-db 3306
#



# MTAP Deployment Commands

### Reusing the Maven local repository
* docker volume create --name maven-repo
* docker run -it -v maven-repo:/root/.m2 maven:3-jdk-11 mvn archetype:generate # will download artifacts
#

### Build project 
##### locally
* mvn clean install -DskipTests

##### using docker image
* docker run -it --rm --name maven-mtap -v maven-repo:/root/.m2 -v /c/Users/pj337/mtap:/usr/src/mtap -w /usr/src/mtap maven:3-jdk-11 mvn clean install -X -DskipTests
#

### Create docker image
##### using docker-cli
* docker image build -t pjramores/mtap-server .
##### using maven on local docker daemon
* mvn package -Pjib
##### using maven on remote registry
* mvn jib:build -Pjib
#

### Create docker container
* docker run -p 8081:8080 --name mtap-server pjramores/mtap-server
##### cleanup container
* docker container stop mtap-server
* docker container rm mtap-server
#

### Deploy to k8s using kubectl
* kubectl create -f manifest/deployment.yml
* kubectl get deployment,svc,pods
##### cleanup deployment
* kubectl delete -f manifest/deployment.yml
#

### Deploy to k8s as helm chart
#
