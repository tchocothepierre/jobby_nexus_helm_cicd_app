pipeline{

     agent any
     environment {

        VERSION = "${env.BUILD_ID}"
     }

     stages{

        stage('sonar quality status'){

            agent{

                docker{
                    image 'maven'
                }
            }
            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonar-token') {

                        sh 'mvn clean package sonar:sonar'
                }
                    
                }
            }
        }

stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                    }
                }
            }
		
        stage('docker build & docker push to nexus repo'){

            steps{

                script{

                    withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_creds')]) {
                        sh '''
                         docker build -t 54.86.49.51:8083/springapp:${VERSION} .

                        docker login -u admin -p $nexus_creds 54.86.49.51:8083

                        docker push 54.86.49.51:8083/springapp:${VERSION}

                        docker rmi 54.86.49.51:8083/springapp:${VERSION}
                         '''
                    }
                    
                }
            }
        }

     }

}