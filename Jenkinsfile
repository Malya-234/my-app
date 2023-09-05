node{
   stage('SCM Checkout 4'){
     git 'https://github.com/udhayakumar2507/my-app'
   }
   stage('maven-buildstage'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   
   stage('SonarQube Analysis 1') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
    
   stage('Build Docker Image'){
   sh 'docker build -t smalya/webapp .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u smalya -p ${dockerPassword}"
    }
   sh 'docker push smalya/webapp'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 100.25.110.251:8083"
   sh "docker tag smalya/webapp 100.25.110.251:8083/dockerprivate:1.0.0"
   sh 'docker push 100.25.110.251:8083/dockerprivate:1.0.0'
   }
    
       stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
    stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest smalya/webapp' 
   }
   }
}
