
pipeline {
    agent any
    //  CI_jenkinsfile
    tools{
        jdk 'jdk11'
        maven 'maven'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/faiz2020/Ekart.git'
            }
        }
        stage('Compile'){
            steps {
                sh "mvn clean compile"
            }
        }
        stage('SonarQube Analysis'){
            steps {
                sh '''
                 $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.host.url=http://98.81.156.180:9000 \
                    -Dsonar.login=squ_cc0821f4dde9b78d0d61882beca4475d69a55298 \
                    -Dsonar.projectKey=Ekart \
                    -Dsonar.projectName=Ekart \
                    -Dsonar.java.binaries=. \
                    -Dsonar.scanner.provisioning.skip=true
                '''
            }
        }
        stage('OWASP SCAN'){
             steps {
        dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
    }
    post {
        always {
            dependencyCheckPublisher pattern: '**/dependency-check-report.html'
        }
    }

        }
        stage('Build app'){
            steps {
                sh "mvn clean install -DskipTests=true"
            }
        }
        stage('docker build and push'){
            steps {
                script{
                    try {
        withDockerRegistry(credentialsId: 'bd5a1964-250e-40cb-a75a-2415b95a5f1a', toolName: 'docker') {
            sh "docker build -t shopping:latest -f docker/Dockerfile ."
            sh "docker tag shopping:latest faiz792/shopping:latest"
            sh "docker push faiz792/shopping:latest"
        }
    } catch (err) {
        echo "Docker stage failed: ${err}"
    }

                }
            }
        }
    }
}
