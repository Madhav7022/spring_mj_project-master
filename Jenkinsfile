pipeline {
    agent any

    tools {
        maven 'maven' // Ensure this matches Global Tool Configuration
    }

    environment {
        // CHANGE THESE TO MATCH YOUR REAL DETAILS
        REMOTE_USER = 'deployer'
        REMOTE_HOST = '192.168.1.50' 
        JAR_NAME    = 'spring_app_sak-0.0.1-SNAPSHOT.jar'
        DEPLOY_PATH = '/opt/apps/sak_app'
        CRED_ID     = 'prod-server-ssh-key' // ID from Jenkins Credentials
    }

    stages {
        stage('Build') {
            steps {
                // Clean workspace first to ensure no stale files
                cleanWs() 
                echo "Building Artifact..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy to Production') {
            steps {
                sshagent([CRED_ID]) {
                    // 1. Transfer the JAR to Server 2
                    sh """
                        echo "Transferring JAR to Production Server..."
                        scp -o StrictHostKeyChecking=no target/${JAR_NAME} ${REMOTE_USER}@${REMOTE_HOST}:${DEPLOY_PATH}/app.jar
                    """
                    
                    // 2. Restart the Service on Server 2
                    // This replaces your fragile 'pkill' and 'java -jar &' logic
                    sh """
                        echo "Restarting Systemd Service..."
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} 'sudo systemctl restart sak-app.service'
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful. Service restarted."
        }
        failure {
            echo "❌ Deployment Failed. Check console logs."
        }
        always {
            cleanWs()
        }
    }
}
