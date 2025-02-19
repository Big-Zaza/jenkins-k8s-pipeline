pipeline {
    agent any

    environment {
        IMAGE_NAME = 'bigzaza/pythonapp'
        IMAGE_TAG = "${IMAGE_NAME}:${env.GIT_COMMIT}"
        KUBECONFIG = credentials('kubeconfig-credentials-id')
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
        
    }

    
    stages {
        stage('Setup') {
            steps {
                sh 'ls -la $KUBECONFIG'
                sh 'chmod 644 $KUBECONFIG'
                sh 'ls -la $KUBECONFIG'
                //sh "pip install -r requirements.txt"
            }
        }
        stage('Test') {
            steps {
                echo 'python tests are being carried out'
                //sh "pytest"
            }
        }

        stage('Login to docker hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh 'echo ${PASSWORD} | docker login -u ${USERNAME} --password-stdin'}
                echo 'Login successfully'
            }
        }

        stage('Build Docker Image')
        {
            steps
            {
                sh 'docker build -t ${IMAGE_TAG} .'
                echo "Docker image build successfully"
                sh 'docker image ls'
                
            }
        }

        stage('Push Docker Image')
        {
            steps
            {
                sh 'docker push ${IMAGE_TAG}'
                echo "Docker image push successfully"
            }
        }

        stage('Deploy to Staging')
        {
            steps {
                sh 'kubectl config use-context arn:aws:eks:ca-central-1:303825583210:cluster/class26'
                sh 'kubectl config current-context'
                sh "kubectl set image deployment/flask-app flask-app=${IMAGE_TAG}"
                echo "New image of python app has been deployed"
            }
        }
/*
        stage('Acceptance Test')
        #{
        #    steps {

        #        script {

        #            def service = sh(script: "kubectl get svc flask-app-service -o jsonpath='{.status.loadBalancer.ingress[0].hostname}:{.spec.ports[0].port}'", returnStdout: true).trim()
        #            echo "${service}"

        #            sh "k6 run -e SERVICE=${service} acceptance-test.js"
        #        }
        #    }
        #}
        
        #stage('Deploy to Prod')
        #{
        #    steps {
        #        sh 'kubectl config use-context kubectl config use-context arn:aws:eks:ca-central-1:303825583210:cluster/class26'
        #        sh 'kubectl config current-context'
        #        sh "kubectl set image deployment/flask-app flask-app=${IMAGE_TAG}"
        #    }
        #}       
*/
        
    }
}
