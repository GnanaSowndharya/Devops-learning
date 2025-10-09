pipeline {
    agent {
        kubernetes {
            label 'slave'
        }
    }
    environment {
        // Map branches to GCP projects
        DEV_PROJECT  = "dev-project"
        UAT_PROJECT  = "uat-project"
        PROD_PROJECT = "prod-project"

        // VM details (you can parameterize further)
        VM_NAME = "prod-vm"
        ZONE    = "asia-south1-b"
    }

    stages {
        stage('Checkout') {
            steps {
                // Jenkins multibranch pipeline auto-checks out the branch
                checkout scm
                echo "Checked out branch: ${env.BRANCH_NAME}"
            }
        }

        stage('Build') {
            steps {
                echo "Running build for branch: ${env.BRANCH_NAME}"
                sh "ls -l"   // Replace with build steps if required
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == "dev") {
                        echo "Deploying to DEV project..."
                        sh """
                          gcloud config set project ${DEV_PROJECT}
                          gcloud compute scp -r ./index.html ${VM_NAME}:~/ --zone=${ZONE}
                          gcloud compute ssh ${VM_NAME} --zone=${ZONE} --command "sudo mv ~/index.html /var/www/html/index.html"
                        """
                    } else if (env.BRANCH_NAME == "uat") {
                        echo "Deploying to UAT project..."
                        sh """
                          gcloud config set project ${UAT_PROJECT}
                          gcloud compute scp -r ./index.html ${VM_NAME}:~/ --zone=${ZONE}
                          gcloud compute ssh ${VM_NAME} --zone=${ZONE} --command "sudo mv ~/index.html /var/www/html/index.html"
                        """
                    } else if (env.BRANCH_NAME == "master") {
                        echo "Deploying to PROD project..."
                        sh """
                          gcloud config set project ${PROD_PROJECT}
                          gcloud compute scp -r ./index.html ${VM_NAME}:~/ --zone=${ZONE}
                          gcloud compute ssh ${VM_NAME} --zone=${ZONE} --command "sudo mv ~/index.html /var/www/html/index.html"
                        """
                    } else {
                        echo "Branch ${env.BRANCH_NAME} not configured for deployment."
                    }
                }
            }
        }
    }
}

