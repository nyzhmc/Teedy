// pipeline {
//   agent any
//   stages {
//     stage('Build') {
//       steps {
//         sh 'mvn -B -DskipTests clean package'
//       }
//     }
//     stage('doc') {
//       steps {
//         sh 'mvn javadoc:jar --fail-never'
//       }
//     }
//     stage('pmd') {
//       steps {
//         sh 'mvn pmd:pmd'
//       }
//     }
//     stage('Test'){
//       steps {
//         sh 'mvn -Dtest=TestCss test'
//       }
      
//     }
//   }  
//   post {
//     always {
//       archiveArtifacts artifacts: '**/target/site/**', fingerprint: true
//       archiveArtifacts artifacts: '**/target/**/*.jar', fingerprint: true
//       archiveArtifacts artifacts: '**/target/**/*.war', fingerprint: true
//       archiveArtifacts artifacts: '**/target/surefire-reports/**', fingerprint: true
//     }  
//   }
// }
pipeline {
    agent any

    environment {
        // Docker repository credentials ID in Jenkins
        DOCKER_CREDENTIALS_ID = 'dockerhub_credentials'
        // Name of the Docker image to be pushed
        IMAGE_NAME = 'nyzhmc/teedy-docker'
        // Tag for the Docker image
        TAG = 'v1.0'
    }

    stages {
        // Stage to check out code and package the application
        stage('Package') {
            steps {
                checkout scm
                sh 'mvn -B -DskipTests clean package'
            }
        }

        // Stage to build the Docker image
        stage('Build Image') {
            steps {
                // Build Docker image using Dockerfile in the current directory
                sh "sudo docker build -t ${IMAGE_NAME}:${TAG} -f Dockerfile ."
            }
        }

        // Stage to push the Docker image to Docker Hub
        stage('Push Image') {
            steps {
                // Login to Docker Hub
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "sudo docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    // Push Docker image
                    sh "sudo docker push ${IMAGE_NAME}:${TAG}"
                }
            }
        }

        // Stage to stop and remove previous Docker containers
        stage('Stop and Remove Previous Containers') {
            steps {
                // Stop and remove container named teedy_01
                sh "sudo docker stop teedy_01 || true"
                sh "sudo docker rm teedy_01 || true"
                // Stop and remove container named teedy_02
                sh "sudo docker stop teedy_02 || true"
                sh "sudo docker rm teedy_02 || true"
                // Stop and remove container named teedy_03
                sh "sudo docker stop teedy_03 || true"
                sh "sudo docker rm teedy_03 || true"
            }
        }

        // Stage to run new Docker containers
        stage('Run Containers') {
            steps {
                // Run new containers with the specified image and tag
                sh "sudo docker run -d -p 8084:8080 --name teedy_01 ${IMAGE_NAME}:${TAG}"
                sh "sudo docker run -d -p 8082:8080 --name teedy_02 ${IMAGE_NAME}:${TAG}"
                sh "sudo docker run -d -p 8083:8080 --name teedy_03 ${IMAGE_NAME}:${TAG}"
            }
        }
    }
}
