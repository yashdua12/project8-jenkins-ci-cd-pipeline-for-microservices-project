pipeline {
    agent any
    tools {
        jdk 'java17'
        maven 'maven'
    }

    environment {
        // Slack
        SLACK_CHANNEL = 'petclinic'
        SLACK_CREDENTIALS = 'slack01'

        // Docker Hub
        DOCKERHUB_CREDENTIALS = 'dockerhub01'
        DOCKERHUB_REPO = 'yashduaa/petclinic-hub'

        // Deployment EC2
        DEPLOYMENT_USER = 'ubuntu'
        DEPLOYMENT_HOST = 'instance deployment ec2 elastic ip'
        DEPLOYMENT_KEY = 'deploy05'
    }

    stages {
        stage('Clone Repository on Deployment EC2') {
            steps {
                sshagent([env.DEPLOYMENT_KEY]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOYMENT_USER}@${DEPLOYMENT_HOST} '
                            export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 &&
                            export PATH=\$JAVA_HOME/bin:\$PATH &&
                            export M2_HOME=/opt/maven &&
                            export PATH=\$M2_HOME/bin:\$PATH &&
                            git clone -b main https://github.com/spring-petclinic/spring-petclinic-microservices.git || true
                        '
                    """
                }
            }
        }

        stage('Test Code with Semgrep on Deployment EC2') {
            steps {
                sshagent([env.DEPLOYMENT_KEY]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOYMENT_USER}@${DEPLOYMENT_HOST} '
                            export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 &&
                            export PATH=\$JAVA_HOME/bin:\$PATH &&
                            export M2_HOME=/opt/maven &&
                            export PATH=\$M2_HOME/bin:\$PATH &&
                            cd spring-petclinic-microservices &&
                            semgrep --config=p/ci --json > semgrep.txt || true
                        '
                    """
                }
            }
        }

        stage('Slack Notification: Semgrep Report') {
            steps {
                slackUploadFile(
                    channel: env.SLACK_CHANNEL,
                    filePath: 'semgrep.txt',
                    initialComment: "🔍 Semgrep scan completed on Deployment EC2. Bugs & vulnerabilities report attached."
                )
            }
        }

        stage('Build Code & Docker Images on Deployment EC2') {
            steps {
                sshagent([env.DEPLOYMENT_KEY]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOYMENT_USER}@${DEPLOYMENT_HOST} '
                            export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 &&
                            export PATH=\$JAVA_HOME/bin:\$PATH &&
                            export M2_HOME=/opt/maven &&
                            export PATH=\$M2_HOME/bin:\$PATH &&
                            cd spring-petclinic-microservices &&
                            mvn clean install -P buildDocker
                        '
                    """
                }
            }
        }

        stage('Deploy Containers on Deployment EC2') {
            steps {
                sshagent([env.DEPLOYMENT_KEY]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOYMENT_USER}@${DEPLOYMENT_HOST} '
                            cd spring-petclinic-microservices &&
                            docker-compose up -d
                        '
                    """
                }
            }
        }

        stage('Slack Notification: Deployment Successful') {
            steps {
                slackSend(
                    channel: env.SLACK_CHANNEL,
                    message: "✅ Deployment successful! Spring PetClinic Microservices are live on Deployment EC2."
                )
            }
        }
    }
}
