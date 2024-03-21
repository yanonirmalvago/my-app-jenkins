node{
   stage('SCM CHECKOUT'){
     git 'https://github.com/VM2322/my-app.git'
   }
      stage('BUILD-MAVEN'){
      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SONARQUBE ANALYSIS') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
      stage('DOCKER IMAGE BUILD'){
   sh 'docker build -t vm2322/myweb:0.0.2 .'
   }
      stage('DOCKERHUB PUSH'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u vm2322 -p ${dockerPassword}"
    }
   sh 'docker push vm2322/myweb:0.0.2'
   }
     stage('NEXUS ARTIFACTORY'){
   sh "docker login -u admin -p nexus456 54.255.169.137:8083"
   sh "docker tag vm2322/myweb:0.0.2 54.255.169.137:8083/vivek:1.0.0"
   sh 'docker push 54.255.169.137:8083/vivek:1.0.0'
   }
      stage('REMOVE PREVIOUS CONTAINER'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('DOCKER DEPLOYMENT'){
   sh 'docker run -d -p 8090:8080 --name tomcattest vm2322/myweb:0.0.2' 
   }
}
}
