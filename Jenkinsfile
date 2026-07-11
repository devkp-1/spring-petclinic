pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/devkp-1/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh './mvnw test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh './mvnw sonar:sonar'
                }
            }
        }

        stage('ZAP Security Scan') {
            steps {
                sh '''
                    docker exec zap zap-baseline.py \
                        -t http://192.168.252.2:8080 \
                        -I || true
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh 'ansible-playbook -i /var/jenkins_home/ansible/inventory.ini /var/jenkins_home/ansible/playbook.yml -e "jar_file=/var/jenkins_home/workspace/spring-petclinic/target/spring-petclinic-4.0.0-SNAPSHOT.jar"'
            }
        }
    }

    post {
        success {
            echo 'pipeline success'
        }
        failure {
            echo 'pipeline failure'
        }
    }
}