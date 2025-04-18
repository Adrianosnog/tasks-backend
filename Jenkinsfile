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
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_5b9ed2ea476a1d5179214ad5568452d2803b982d -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn**,**/src/test/**,**/model/**,**Application.java -Dsonar.qualitygate.wait=true"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                sleep(5)
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Deploy Backend'){
            deploy adapters: [tomcat8(credentialsId: 'github_adriano', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
        }
    }
}

