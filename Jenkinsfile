pipeline {
    agent any
    
	tools {
        maven 'localMaven'
    }
	
    parameters { 
         string(name: 'tomcat_dev', defaultValue: 'ec2-52-14-198-9.us-east-2.compute.amazonaws.com', description: 'StagingServer')
         string(name: 'tomcat_prod', defaultValue: 'http://localhost:8081', description: 'ProductionServer')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /home/mazen/toolsForJenkins/pipelinecode/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat8/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/mazen/toolsForJenkins/pipelinecode/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat8/webapps"
                    }
                }
            }
        }
    }
}