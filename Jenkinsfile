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
        docker.withRegistry("","docker-hub"){
          sh 'docker build -t raziahmed/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push raziahmed/numeric-app:""$GIT_COMMIT""'
      }
      }
    }
    }
}
  
  