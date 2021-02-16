pipeline {
    agent any
    stages {
        stage ('SCM checkout') {
            git ''
        }
               
        stage ('Build Docker Image') {
            //Using docker plugin
            docker.build("cloudseals/node-app:latest")
        }
    
    
        stage ('Docker Smoke Test') {
            sh '''
            res=$(curl -Is "http://localhost:8080/" | head -1)
            if [[ "$res" == "HTTP/1.1 200 OK" ]]
            then
                echo "Pushing docker image to ECR...!"
            else
                echo "Smoke test failed, please verify your docker file and the application code!"
            fi
            '''
        }
        stage ('Push Docker Image to ECR') {
            //cleanup current user docker credentials
            sh 'rm  ~/.dockercfg || true'
            sh 'rm ~/.docker/config.json || true'
            //Using docker plugin to push to AWS ECR
            docker.withRegistry("https://453093980721.dkr.ecr.us-east-2.amazonaws.com", "ecr:us-east-2:3d026207-85a4-45bc-abd4-501c2fd07fa0") {
            docker.image("cloudseals/node-app:latest").push()
            }
        }
        
        stage ('CleanUP')
        {
            cleanWs()
        }
    }
    }
