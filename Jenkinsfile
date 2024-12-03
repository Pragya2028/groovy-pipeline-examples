pipeline {
    agent any  // Runs on any available agent

    environment {
        // Define SonarQube server details (optional if already set in Jenkins config)
        SONARQUBE_SERVER = 'Sonarqube 9.9.7'  // This should match the name of your SonarQube server in Jenkins config
        SONARQUBE_SCANNER = 'Maven'   // You can configure the tool globally in Jenkins if needed
    }

    stages {
        stage('Checkout Code') {
            steps {
                  checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],  // Change this to the branch you need (e.g., main, develop)
                    userRemoteConfigs: [[url: 'https://github.com/Pragya2028/groovy-pipeline-examples.git']]
                  
                ])']]
                ])
            }
        }

        stage('Build') {
            steps {
                script {
                    // Example of a Maven build, adjust as necessary for your project (e.g., Gradle, Node.js, etc.)
                    sh 'mvn clean install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube analysis using the SonarQube Scanner
                    withSonarQubeEnv(SONARQUBE_SERVER) {
                        sh "${SONARQUBE_SCANNER}/bin/sonar-scanner -Dsonar.projectKey=demoapp-project -Dsonar.sources=src"
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
  script {
              def qualityGate = waitForQualityGate()  // Wait for SonarQube Quality Gate status
            if (qualityGate.status != 'OK') {
                def issues = qualityGate.conditions.find { it.metricKey == 'vulnerabilities' }
                if (issues) {
                    error "Quality Gate failed due to vulnerabilities"
                } else {
                    error "Quality Gate failed: ${qualityGate.status}"
                }
            }
        }
    }
}

        stage('Deploy') {
            steps {
                 echo 'Deploying to remote server using credentials...'

            // Use environment variables for sensitive data
            sh "scp -i ${env.ssh -i "demo.pem" ec2-user@ec2-13-50-245-64.eu-north-1.compute.amazonaws.com} target/my-app.war ${env.ec2-user}@${env.13.50.245.64}:/path/to/deploy"
        }
                }
            }
        }
    }

    post {
        success {
            echo 'Build and SonarQube analysis completed successfully!'
        }
        failure {
            echo 'Build failed or SonarQube analysis failed!'
        }
    }
}
