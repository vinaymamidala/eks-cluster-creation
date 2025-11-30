pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "us-east-1"
        CLUSTER_NAME = "dev-eks-cluster"
        NODE_TYPE = "t3.medium"
        NODE_COUNT = "2"
    }

    stages {

        stage('Install eksctl') {
            steps {
               sh '''
                curl --silent --location "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C .
                chmod +x eksctl
                export PATH=$PATH:$(pwd)
                ./eksctl version
                 '''
            }
        }

        stage('Configure AWS Credentials') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                                  credentialsId: 'aws-jenkins-creds']]) {
                    sh '''
                    aws sts get-caller-identity
                    '''
                }
            }
        }

        stage('Create EKS Cluster') {
            steps {
                sh '''
                eksctl create cluster \
                  --name ${CLUSTER_NAME} \
                  --region ${AWS_DEFAULT_REGION} \
                  --node-type ${NODE_TYPE} \
                  --nodes ${NODE_COUNT} \
                  --nodes-min 1 \
                  --nodes-max 3 \
                  --managed
                '''
            }
        }

    }

    post {
        always {
            echo "Pipeline Finished"
        }
        // OPTIONAL: Uncomment this to delete the cluster after pipeline
        /*
        cleanup {
            sh '''
            eksctl delete cluster --name ${CLUSTER_NAME} --region ${AWS_DEFAULT_REGION}
            '''
        }
        */
    }
}

