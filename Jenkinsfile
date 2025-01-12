pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    
    }

    //environment {
    //    SCANNER_HOME = tool 'SonarQube Scanner'
    //}

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shantanudatarkar/Ekart.git'
            }
        }

        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }

        // stage('OWASP Scan') {
        //    steps {
        //        dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
        //        dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //    }
        // }

        // stage('Static Code Analysis') {
        //     environment {
        //         SONAR_URL = "http://54.252.172.0:9000/"
        //     }
        //     steps {
        //         withCredentials([string(credentialsId: 'sonarQube-token', variable: 'sonarQube')]) {
        //             echo "SonarQube Token: ${sonarQube}"
        //             sh 'mvn sonar:sonar -Dsonar.login=$sonarQube -Dsonar.host.url=${SONAR_URL}'
        //         }
        //     }
        // }

        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/') {
                        def buildNumber = env.BUILD_NUMBER ?: 'latest'
                        sh "docker build -t shan123456/docker_demo -f docker/Dockerfile ."
                        sh "docker tag shan123456/docker_demo:latest shan123456/docker_demo:${buildNumber}"
                        sh "docker push shan123456/docker_demo:${buildNumber}"
                        sh "docker push shan123456/docker_demo:latest"
                    }
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                script {
                    kubeconfig(credentialsId: 'kubernetes', serverUrl: '') {
                        sh "kubectl delete pods --all"
                        sh "kubectl apply -f deploymentservice.yml"
                    }
                }
            }
        }
    }
}

//Gunjan Kakade
