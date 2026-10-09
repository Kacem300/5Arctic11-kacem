pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        DOCKER_IMAGE = "kacembenali_5SIM1_DevOps-AppGestionDesProjets"   // ⚠️ EDIT: nomprenom_classe_nomProjet
        DOCKER_CREDS = credentials('dockerhub-creds')
        SONAR_TOKEN  = credentials('sonar-token')
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Pulling code from Git...'
                git branch: 'main',
                    url: 'https://github.com/YOUR_USERNAME/5SIM1-kacem.git'   // ⚠️ EDIT: your repo URL
            }
        }

        stage('Build (Maven)') {
            steps {
                dir('backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('backend') {
                    withSonarQubeEnv('SonarQube') {
                        sh '''
                            mvn org.sonarsource.scanner.maven:sonar-maven-plugin:4.0.0.4121:sonar \
                              -Dsonar.projectKey=DevOps-AppGestionDesProjets \
                              -Dsonar.host.url=http://localhost:9000
                        '''
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    sh "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."
                    sh "docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "echo \$DOCKER_CREDS_PSW | docker login -u \$DOCKER_CREDS_USR --password-stdin"
                sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                sh "docker push ${DOCKER_IMAGE}:latest"
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed. Check the console log.'
        }
    }
}
