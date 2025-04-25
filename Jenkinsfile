pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Test'){
            steps{
                bat 'mvn test'
                }
            }
        stage('Sonar Analysis'){
            environment {
                scannerHome = tool 'Sonar_Scanner'
            }
            steps{
                withSonarQubeEnv('Sonar_Local'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_008213ea75bc4e88218477dca07d041ce550d90b -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn**,**/src/test/**,**/model/**,**Application.java -Dsonar.qualitygate.wait=true"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                // sleep(20)
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'Login_Servidor', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('Api Test'){
            steps{
                dir('api-test'){
                    git credentialsId: 'github_adriano', url: 'https://github.com/Adrianosnog/tasks.api.test'
                    bat 'mvn test'
                    }
            }
        }
        stage ('Deploy Frontend'){
                steps{
                    dir('frontend'){
                        git credentialsId: 'github_adriano', url: 'https://github.com/Adrianosnog/tasks-frontend'
                        bat 'mvn clean package -DskipTests=true'
                        deploy adapters: [tomcat8(credentialsId: 'Login_Servidor', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage ('Functional Test'){
                steps{
                    dir('test-front'){
                        git credentialsId: 'github_adriano', url: 'https://github.com/Adrianosnog/tasks.frontend.test'
                        bat 'mvn test'
                }
            }
        }
        stage('Deploy Prod'){
            steps{
                bat 'docker-compose build'
                bat 'docker-compose up -d'
            }
        }
    }
}

