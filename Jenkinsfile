pipeline {
   agent any

   stages {
     stage('Polling SCM' ) {
         steps {
            echo 'Cloning code from remote repository'
	    sh '''
            cd /tmp/
	    mkdir icon || 'true'
            cd icon
            git clone https://github.com/deji-bit/myweatherapp.git
            '''
      	 }  
       }
      stage('Compile' ) {
         steps {
            echo 'Compiling code...'
	    sh '''
            cd /tmp/icon/myweatherapp
            mvn clean package
            '''
      	 }  
       }
      stage('Build' ) {
         steps {
            echo 'Copying SNAPSHOT to remote app node'
            sh '''
            sudo ssh -o StrictHostKeychecking=no -i ~/.ssh/first_keys ec2-user@10.0.0.170 'rm -r /tmp/weather-app-0.0.1-SNAPSHOT.jar' || 'true'
            sudo scp -v -i ~/.ssh/first_keys /tmp/icon/myweatherapp/target/weather-app-0.0.1-SNAPSHOT.jar ec2-user@10.0.0.170:/tmp/
            '''
      	 }
       }
      stage('Deploy' ) {
         steps {
            echo 'Deploying code to non-active node'
	    sh '''
	    sudo ssh -i ~/.ssh/first_keys ec2-user@10.0.0.170 '
            cd /tmp/
            java -jar weather-app-0.0.1-SNAPSHOT.jar &
            exit '
            pwd
            '''
      	 }
       }
      stage('Cleanup' ) {
         steps {
            echo 'Removing used files and folders'
	    sh '''
	    sudo rm -rf /tmp/icon/
            '''
      	 }
       }
    }  
}
