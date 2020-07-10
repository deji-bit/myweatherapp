pipeline {
   agent any

   stages {
     stage('Polling SCM' ) {
         steps {
            echo 'Cloning code from remote repository'
	    sh '''
            cd /tmp/
	    rm -rf icon || 'true'
	    mkdir icon
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
            sudo ssh -o StrictHostKeychecking=no -i /home/ec2-user/.ssh/first_keys ec2-user@10.0.0.144 'rm -r /tmp/weather-app-0.0.1-SNAPSHOT.jar' || 'true'
            sudo scp -v -i /home/ec2-user/.ssh/first_keys /tmp/icon/myweatherapp/target/weather-app-0.0.1-SNAPSHOT.jar ec2-user@10.0.0.144:/tmp/
            '''
      	 }
       }
      stage('Set Proxy' ) {
         steps {
            echo 'Configuring Proxy Server for app deployment'
            sh '''
            sudo scp -v -o StrictHostKeychecking=no -i /home/ec2-user/.ssh/first_keys /tmp/icon/myweatherapp/nginx.conf ec2-user@10.0.0.250:/tmp/
	    cd
	    sudo ssh -i /home/ec2-user/.ssh/first_keys ec2-user@10.0.0.250 '
	    sudo systemctl stop nginx
            cd /tmp/
            sudo cp nginx.conf /etc/nginx/
	    sudo systemctl start nginx
	    sudo systemctl daemon-reload '
            '''
      	 }
       }
      stage('Deploy' ) {
         steps {
            echo 'Deploying code to non-active node'
	    sh '''
	    sudo ssh -i /home/ec2-user/.ssh/first_keys ec2-user@10.0.0.144 '
	    kill pid 30240
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
