node{
   stage('SCM Checkout from GitHub'){
     git 'https://github.com/kavi54123/my-app'
   }
   stage('Building war file using Maven'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Building Docker Image'){
   sh 'docker build -t kavi2512/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u kavi2512 -p ${dockerPassword}"
    }
   sh 'docker push kavi2512/myweb:0.0.2'
   }
   
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.250.2.132:8082"
   sh "docker tag saidamo/myweb:0.0.2 13.250.2.132:8082/kavi:1.0.0"
   sh 'docker push 13.250.2.132:8082/kavi:1.0.0'
   }

   stage('Removing Previous Container if any!'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 1100:8080 --name tomcattest saidamo/myweb:0.0.2' 
   }
}
}
