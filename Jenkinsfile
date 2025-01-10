pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'arunk369/nodejs-app'
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'
        BUILD_NUMBER = "${env.BUILD_NUMBER}"  // Unique tag for the image
        TRIVY_IMAGE = 'aquasec/trivy'
        KUBECONFIG_CREDENTIALS = 'kubeconfig-credentials' 
        HELM_CHART_DIR = 'nodejs-application'
        RELEASE_NAME = 'nodejs-app'
        NAMESPACE = 'default'
        KUBE_BENCH_IMAGE = 'aquasec/kube-bench'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Arunkumar2255/WEBAPP.git'
            }
        }

        stage('Sonarqube Analysis') {
            steps {
                sh '''
                $SCANNER_HOME/bin/sonar-scanner -Dsonar.host.url=http://43.204.140.147:9000/ -Dsonar.login=squ_385a453cdf3c2d510c66a2093be3be53e23ecf93 -Dsonar.projectName=nodejs-app \
                -Dsonar.sources=. \
                -Dsonar.projectKey=nodejs-app
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build --no-cache -t $DOCKER_IMAGE:${BUILD_NUMBER} .
                docker tag $DOCKER_IMAGE:${BUILD_NUMBER} $DOCKER_IMAGE:latest
                '''
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker run --rm $DOCKER_IMAGE:${BUILD_NUMBER} cat /home/node/app/app.js'
            }
        }

        stage('Authenticate to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                sh '''
                docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                docker push $DOCKER_IMAGE:latest
                '''
            }
        }

        stage('Install and Scan with Trivy') {
            steps {
                sh '''
                docker pull $TRIVY_IMAGE
                docker run --rm -v /var/run/docker.sock:/var/run/docker.sock $TRIVY_IMAGE image $DOCKER_IMAGE:${BUILD_NUMBER}
                '''
            }
        }

        stage('Helm Deploy') {
            steps {
                sh '''
                helm upgrade --install $RELEASE_NAME $HELM_CHART_DIR \
                --set image.repository=$DOCKER_IMAGE \
                --set image.tag=${BUILD_NUMBER} \
                --set image.pullPolicy=Always \
                --namespace $NAMESPACE --create-namespace
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get pods -n $NAMESPACE
                kubectl get svc -n $NAMESPACE
                '''
            }
        }
        stage('Run kube-bench') {
            steps {
                sh '''
                docker run --rm --pid=host --net=host -v /etc:/etc:ro -v /var/lib:/var/lib:ro \
                -v /usr/bin:/usr/bin:ro -v /var/run:/var/run:ro aquasec/kube-bench \
                --version 1.23 
                '''
            }
        }
    }
}
