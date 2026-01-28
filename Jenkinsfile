pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    echo "Stopping existing Spring Boot application if running..."
                    if pgrep -f spring_app_sak-0.0.1-SNAPSHOT.jar > /dev/null; then
                        sudo pkill -f spring_app_sak-0.0.1-SNAPSHOT.jar
                        echo "Application stopped."
                    else
                        echo "No existing application running."
                    fi

                    echo "Starting the Spring Boot application..."
                    sudo java -jar target/spring_app_sak-0.0.1-SNAPSHOT.jar > /dev/null 2>&1 &
                '''
            }
        }
    }
    post {
        success {
            echo "Deployed successfully"
        }
        failure {
            echo "Failed to Deploy"
        }
    }
}
