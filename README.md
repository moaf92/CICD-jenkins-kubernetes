# CICD-jenkins-kubernetes
this project envolves about continuios delivery of docker containers we will build docker images and deploy it on kubernetes cluster, in this project we will use k8s orchestration tool ,docker container runtime , jenkins for CICD dockerhub which is container registry where we will host our docker images, helm for packing and deploying on k8s cluster, maven to build our code, and sonarqube for analytics
# Flow of execution
- CI setup "jenkins, SonarQube"
- store dockrhub credentials in jenkins
- setup docker engine in jenkins
- install plugins "docker pipeline - docker -pipeline utility"
- create k8s cluster with kops
- create helm charts
- add kops vm as jenkins slave
- create our pipeline code
# STEPS

- Create Jenkins server on aws using ec2 instance with security group allowing inbound trrafic on port 8080 and 22 for ssh , and security group of sonar . and install Jenkins using user data , after this 
  we have to 'cat' to '/var/lib/jenkins/secrets/initialAdminPassword' this file contain initial password to unlock jenkins,configure tools after installing them as plugins and mention it as global 
  configuration . we will need 'oracle jdk 11' and mention its path '/usr/lib/jvm/java-1.11.0-openjdk-amd64' and 'maven', sonarQube 

- Create SonarQube server on aws using ec2 instance with security group allowing inbound trrafic on port 9000 and 22 for ssh and from the security group of jenkins. install SonarQube server using user 
  data, in SQ we will generate token and add save it on jenkins as a credential with mentioning the private ip of the sonar sever, after that create a webhook with giving the private ip of jenkins 

- Store our dockerhub login into jenkins credentials as username and password, after this we will install docker engine in jenkins server,and add our jenkins user to docker group, after this rebbot your 
  jenkins server

- Install dockerpipeline,docker,pipeline utility plugins

- Now time for installing our cluster through kops by creating instance and ssh into it and run this  ' kops create cluster --name=kubevpro.groophy.in \ --state=s3://vprofile-kop-states --zones=us-east- 
  1a,us-east-1b \ --node-count=2 --node-size=t3.small --master-size=t3.medium --dns-zone=kubevpro.groophy.in \ --node-volume-size=8 --master-volume-size=8', after this we have to install helm on this 
  instance

- In docker image we will build our image, as the artifact generated from our source code will be copied into the location we mention in our docker image,so we can continously deliver our app image

- After this we will create or helm charts by running 'helm create projectcharts' and copy our kubernetes definition files into helm templates folder, to test these helm charts we will create namespace on 
  our k8s cluster and name it test,mention the path of our charts and run 'helm install --namespace test project-stack helm/projectcharts --set appimage=mohamedsharaf92/projectapp:latest'

- Add our helm instance as slave from jenkins by ssh into instance installing open-jdk8 and create a directory at /opt path and change the ownership of that directory to ubuntu user so jenkins can ssh into 
  this folder through ubuntu user, update SG of your helm instance to allow ssh from jenkins instance, now from jenkins server manage nodes we will add our helm instance mentioning the directory we created 
  above at /opt path, and give it a label to mntion it in our pipeline and mention its private ip of helm instance, add its credential ssh username with pass mentioning our ubuntu user with ssh private key

- after setting all these now time to our pipeline    
