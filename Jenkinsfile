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


        stage('Identifying misconfigd using datree in helm charts'){

            steps{

                script{

                      dir('kubernetes/myapp/') {

                        sh 'helm datree test .'

                        }  
                }

            }
        }

     }

     post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "tchocothepierre@gmail.com";  
		}

	}

}