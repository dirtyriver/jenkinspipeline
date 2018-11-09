pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '107.23.220.182', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.209.101.223', description: 'Production Server')
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
                            sh "scp -i /Users/anthony/.aws/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                        }
                    }

                    stage ("Deploy to Production"){
                        steps {
                            sh "scp -i /Users/anthony/.aws/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                        }
                    }
                }
            }
        }
}