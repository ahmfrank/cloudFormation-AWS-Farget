# How to create AWS farget for your Java app
Dokcer-AWS-ECS
Jenkins maven server pull a source code from Github, to build jar/war file,  send to docker host to build docker image and 
Push docker image to AWS ECR repository.
Then, Running aws cloudformation with below yaml files to deploy docker image on AWS Fargate ECS 

* Create AWS ECR repository command
     
  $aws  ecr create-repository  --repository-name  << the name of repository >>
* to loggin to AWS
   
  $aws  ecr  get-login  --no-include-email  |  sh 
* create AWS VPC network Run cloudfomation command     
   $aws cloudformation create-stack --stack-name vpc --template-body file://$PWD/infrs/vpc.yml
* If you want to look and copy repository URL in to ecr describe repository do below command   
  $aws  ecr  describe-repositories  --repository-name  << The name of repository >>
* tag docker image file ECR repository will shows each version 
     
  $docker  tag << the name of image >>   repository_URL/<<Repository_Name>>:v_${BUILD_NUMBER}
  
* Push to ECR
  
  $docker push repository_URL/<<Repository_Name>>:v_${BUILD_NUMBER}
  
* Build IAM roles with iam.yml file , and do command as below
   
  $aws cloudformation create-stack --stack-name iam --template-body file://$PWD/iam.yml --capabilities  CAPABILITY_IAM
  
* Create app-cluster yaml file then run

  $aws cloudformation create-stack --stack-name  app-cluster  file://$PWD/app-cluster.yml

* Run cloudforamtion with api.yml as following
  
  $aws cloudformation create-stack --stack-name api file://$PWD/api.yml
  
Port mapping 80:8080, Container port is 8080, and ALB port is 80
