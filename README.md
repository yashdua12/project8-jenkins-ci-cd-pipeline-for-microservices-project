# project8-jenkins-ci-cd-pipeline-for-microservices-project
deploys a microservices project on docker through jenkins-ci/cd pipeline
1. this is a 5 microservices java based web application
2. services are:-
   -home
   -find owners
   -vetnerians
   -register owner
   -chat box
3. this repo includes a jenkinsfile that have 6 stages
   - 1st stage clone repo
   - 2nd stage test code with python semgrep library
   - 3rd stage send report test report txt file through slack notification
   - 4th stage build code and docker images
   - 5th stage deploy containers
   - 6th stage slack notification for build complete
  4. jennins ui was running on a seperate ec2 instance and the app was deployed on other ec2 instance
  5. how to configure slack, deployment ec2 and python  semgrep
   - for slack- install slack notification plugin
   - for deployment ec2 go to credentials in jenkins and in kind select ssh with private key username give ubuntu if instance ami is ubuntu and paste your .pem file content in secret key
   - for slack go to system give id and kind select secret text paste your secret text that you generated on slack app bot
   - rember install maven 3.9.8 and java version 17 on both jenkins ec2 ans deployment ec2 docker and semgrep on both also

thanku 
