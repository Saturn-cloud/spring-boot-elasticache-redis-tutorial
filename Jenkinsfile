pipeline {
   agent any

   stages {
     stage('Polling SCM' ) {
         steps {
            echo 'Cloning code from remote repository'
	    sh '''
            cd /tmp/
	    mkdir placeholder || true
            cd placeholder/
            git clone https://github.com/Saturn-cloud/spring-boot-elasticache-redis-tutorial.git
            '''
      	 }  
       }
      stage('Compile' ) {
         steps {
            echo 'Compiling code...'
	    sh '''
            cd /tmp/placeholder/spring-boot-elasticache-redis-tutorial
            mvn clean package
            '''
      	 }  
       }
      stage('Build' ) {
         steps {
            echo 'Preparing for code deployment'
            sh '''
            sudo ssh -o StrictHostKeychecking=no -i /home/ec2-user/.ssh/first_keys ec2-user@3.8.84.15 '
            cd /tmp/ 
            rm -r example-0.0.1-SNAPSHOT.jar || true 
            cd /home/ec2-user/redis-stable
            src/redis-cli -c -h rediscluster-repgrp.218wyu.clustercfg.euw2.cache.amazonaws.com -p 6379 '
            '''
      	 }
       }
      stage('Deploy' ) {
         steps {
            echo 'Deploying code to Redis-server'
	    sh '''
            sudo scp -i /home/ec2-user/.ssh/first_keys /tmp/placeholder/spring-boot-elasticache-redis-tutorial/target/example-0.0.1-SNAPSHOT.jar ec2-user@3.8.84.15:/tmp/
	    sudo ssh -tt -i /home/ec2-user/.ssh/first_keys ec2-user@3.8.84.15 '
	    sudo pkill java || true
	    cd /tmp/
	    rm -rf target || true
	    mkdir target
	    cp example-0.0.1-SNAPSHOT.jar target
	    cd target
            nohup java -jar example-0.0.1-SNAPSHOT.jar & '
            '''
      	 }
       }
      stage('Cleanup' ) {
         steps {
            echo 'Removing used files and folders'
	    sh '''
	         cd /tmp/
	         sudo rm -rf placeholder/
          '''
      	 }
       }
    }  
}
