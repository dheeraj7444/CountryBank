pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/dheeraj7444/CountryBank.git'
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                 dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy') {
            steps {
                 sh "trivy fs ."
            }
        }
        
         stage('Build & deploy') {
            steps {
                 sh'docker-compose up -d'
            }
        }
        stage('docker Tag and Push') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
              sh """
                 echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                  docker tag nikitap/countrybank:latest dheeraj7444/countrybank:$BUILD_ID
                  docker push dheeraj7444/countrybank:$BUILD_ID
                  """
                 }  
            }
        }
    }
}
