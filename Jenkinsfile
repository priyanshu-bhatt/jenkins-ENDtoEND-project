pipeline {
    
    agent{
        label "linuxslavenode"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/vimallinuxworld13/jenkins-docker-maven-java-webapp.git'
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        stage('Docker File For Tomcat') {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PWD_LOCAL')]) {
    // some block
    sh 'sudo docker login -u priyanshu18 -p ${DOCKER_HUB_PWD_LOCAL}'
}
                sh 'sudo docker build -t priyanshu18/jenkinstomcat:${BUILD_TAG} .'
                sh 'sudo docker push priyanshu18/jenkinstomcat:${BUILD_TAG}'
            }
            
        }
        stage('Test FOR DEVELOPER IN DOCKER') {
            steps {
                sh 'sudo docker rm -f testfordev'
                sh 'sudo docker run -d -p 8080:8080 --name testfordev priyanshu18/jenkinstomcat:${BUILD_TAG}'
                
            }
            
        }
        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['SSH_PASSWD_QA']) {
    
                    sh 'ssh  -o  StrictHostKeyChecking=no ec2-user@3.111.186.144 sudo docker rm -f testforqa'
                    sh 'ssh ec2-user@3.111.186.144 sudo docker run  -d  -p  8080:8080 --name testforqa   priyanshu18/jenkinstomcat:${BUILD_TAG}'
                }

            }
            
        }
        
        stage('Approval Testing Stage') {
            steps {
                retry(5){
                sh 'curl --silent http://3.111.186.144:8080/java-web-app/ | grep India'
                }
            }
            
        }
        stage('approved') {
            steps {
                
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
        
        stage('Deploy webAPP in Prodiction Test Env') {
            steps {
               
               sshagent(['SSH_PRODUCTION_ENV']) {
    
                    sh 'ssh  -o  StrictHostKeyChecking=no ec2-user@13.126.220.146 sudo docker rm -f finalproduction'
                    sh 'ssh ec2-user@13.126.220.146 sudo docker run  -d  -p  8080:8080 --name finalproduction   priyanshu18/jenkinstomcat:${BUILD_TAG}'
                }

            }
            
        }
        stage('email') {
            steps {
                mail bcc: '', body: 'Good job', cc: '', from: '', replyTo: '', subject: 'Hello Priyanshu This is Test', to: 'priyanshubhatt1892001@gmail.com'
            }
        }
    }
    
}
    
