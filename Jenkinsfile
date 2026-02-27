pipeline {
    agent any

    environment {
        IMAGE_NAME = "arjun22bcs60/arjunsreenivas_2022bcs0060:latest"
        CONTAINER_NAME = "2022bcs0060-test-container"
        PORT = "8000"
    }

    stages {

        stage('Pull Image') {
            steps {
                sh 'docker pull $IMAGE_NAME'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p $PORT:$PORT --name $CONTAINER_NAME $IMAGE_NAME'
            }
        }

        stage('Wait for API') {
            steps {
                sh '''
                for i in {1..10}; do
                  curl -f http://localhost:$PORT/health && break
                  sleep 3
                done
                '''
            }
        }

        stage('Valid Request') {
            steps {
                sh '''
                response=$(curl -s -X POST http://localhost:$PORT/predict \
                  -H "Content-Type: application/json" \
                  -d @right.json)

                echo $response
                echo $response | grep prediction
                '''
            }
        }

        stage('Invalid Request') {
            steps {
                sh '''
                curl -X POST http://localhost:$PORT/predict \
                -H "Content-Type: application/json" \
                -d @wrong.json || true
                '''
            }
        }

        stage('Stop Container') {
            steps {
                sh 'docker rm -f $CONTAINER_NAME'
            }
        }
    }
}