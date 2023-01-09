pipeline {
    agent any
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
        stage('Staging Branch Deploy Code') {
            when {
                branch 'staging'
            }
            steps {
                script {
                    echo "Deploying Code from Staging branch"
                }
            }
        }

        stage('Release Branch Deploy Code') {
            when {
                branch 'release'
            }
            steps {
                script {
                    echo "Deploying Code from Release branch"
                }
            }
        }

        stage('Release Master Deploy Code') {
            when {
                branch 'master'
            }
            steps {
                script {
                    echo "Deploying Code from Master branch"
                }
            }
        }

        // Building Docker images
        stage('Building image') {
            steps{
                script {xxxxxxxxxxx
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
        always {
            sh 'echo "This will always run"'
        }
        success {
            sh 'echo "This will run only if successful"'
        }
        failure {
            emailext attachLog: true, body: 'Fail Test', subject: 'Fail Test', to: 'cheahhowong@gmail.com'
            sh 'echo "This will run only if failed"'
        }
        unstable {
            sh 'echo "This will run only if the run was marked as unstable"'
        }
        changed {
            sh 'echo "This will run only if the state of the Pipeline has changed"'
            sh 'echo "For example, the Pipeline was previously failing but is now successful"'
            sh 'echo "... or the other way around :)"'
        }
    }
}
