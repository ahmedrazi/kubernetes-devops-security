pipeline {
  agent any
  environment {
    DOCKER_CREDENTIALS = credentials('docker-hub')
  }

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        } 
      stage('Unit Tests - JUnit and Jacoco') {
          steps {
              sh "mvn test"
          }
          post {
              always {
                  junit 'target/surefire-reports/*.xml'
                  jacoco execPattern: 'target/jacoco.exec'
              }
          }
      }
      stage('Docker image build and push') {
          steps {
              sh '''
                echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                docker build -t raziahmed/numeric-app:$GIT_COMMIT .
                docker push raziahmed/numeric-app:$GIT_COMMIT
                docker logout
              '''
          }
      }
      stage('Kubernetes Deployment - DEV') {
          steps {
              script {
                  withKubeConfig([credentialsId: 'kubeconfig']) {
                      sh "sed -i 's#replace#raziahmed/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                      sh "kubectl apply -f k8s_deployment_service.yaml"
                  }
              }
          }
      }
}
}





















/*
pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        } 
      stage('Unit Tests - JUnit and Jacoco') {
          steps {
              sh "mvn test"
          }
          post {
              always {
                  junit 'target/surefire-reports/*.xml'
                  jacoco execPattern: 'target/jacoco.exec'
              }
          }
      }
      stage('Docker image build and push') {
          steps {
              script {  // Added script block here
                  docker.withRegistry(url: "", credentialsId: "docker-hub") {  // Fixed credentialsId spelling
                      sh 'docker build -t raziahmed/numeric-app:""$GIT_COMMIT"" .'
                      sh 'docker push raziahmed/numeric-app:""$GIT_COMMIT""'
                  }
              }
          }
      }
  }
}
*/
  