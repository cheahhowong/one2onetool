pipeline {
    agent any
    tools {nodejs "node"}
    environment {
        AWS_ACCOUNT_ID="939612840870"
        AWS_DEFAULT_REGION="ap-southeast-1"
        CLUSTER_NAME="one2onetool-cluster"
        SERVICE_NAME="one2onetool-container-service"
        TASK_DEFINITION_NAME="first-run-task-definition"
        DESIRED_COUNT="1"
        IMAGE_REPO_NAME="one2onetool"
        IMAGE_TAG="${env.BRANCH_NAME}${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
        registryCredential = "admin-user"
    }

    stages {
        // Unit Tests
        stage('Staging Branch Unit Test') {
            when {
                branch 'staging'
            }
            steps {
                script {
                    git 'https://github.com/cheahhowong/one2onetool/tree/staging'
                    sh 'npm install'
                    sh 'npm test -- --watchAll=false'
                }
            }
        }

        stage('Release Branch Unit Test') {
            when {
                branch 'release'
            }
            steps {
                script {
                    git 'https://github.com/cheahhowong/one2onetool/tree/release'
                    sh 'npm install'
                    sh 'npm test -- --watchAll=false'
                }
            }
        }

        stage('Release Master Unit Test') {
            when {
                branch 'master'
            }
            steps {
                script {
                    git 'https://github.com/cheahhowong/one2onetool/tree/master'
                    sh 'npm install'
                    sh 'npm test -- --watchAll=false'
                }
            }
        }

        // Building Docker images
        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }

        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps{
                script {
                    docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps{
                withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                    script {
                        sh "chmod +x -R ${env.WORKSPACE}"
                        sh './script.sh'
                    }
                }
            }
        }
    }

    post {
        failure {
            emailext attachLog: true, body: 'Please refer to the attachment for log', subject: '[Jenkins Pipeline] Build Error', to: 'cheahhowong@gmail.com'
            sh 'echo "This will run only if failed"'
        }
    }
}
