node{
      def dockerImageName= 'dockerahad/javademoapp_$JOB_NAME:$BUILD_NUMBER'
      stage('SCM Checkout'){
         git 'https://github.com/AhadShamim009/java-groovy-docker.git'
      }
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Apache Maven 3.6.3', type: 'maven'   
         sh "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
      }       
     
     stage ('Test'){
         def mvnHome =  tool name: 'Apache Maven 3.6.3', type: 'maven'  
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }
      
     stage('Build Docker Image'){         
           sh "docker build -t ${dockerImageName} ."
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwddockerahad', variable: 'dockerPWDdockerahad')]) {
              sh "docker login -u dockerahad -p ${dockerPWD}"
         }
        sh "docker push ${dockerImageName}"
      }
     
    stage('Run Docker Image'){
            def dockerContainerName = 'javadockerapp_$JOB_NAME_$BUILD_NUMBER'
            def changingPermission='sudo chmod +x stopscript.sh'
            def scriptRunner='sudo ./stopscript.sh'           
            def dockerRun= "sudo docker run -p 8082:8080 -d --name ${dockerContainerName} ${dockerImageName}" 
            withCredentials([string(credentialsId: 'deploymentserverpwd', variable: 'dpPWD')]) {
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no ubuntu@172.29.164.117" 
                  sh "sshpass -p ${dpPWD} scp -r stopscript.sh ubuntu@172.29.164.117:/home/ubuntu" 
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no ubuntu@172.29.164.117 ${changingPermission}"
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no ubuntu@172.29.164.117 ${scriptRunner}"
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no ubuntu@172.29.164.117 ${dockerRun}"
            }
            
      
      }
      
         
  }
      
