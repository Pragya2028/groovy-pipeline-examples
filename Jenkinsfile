pipeline {
    agent any  // Runs on any available agent

    environment {
        // Define SonarQube server details (optional if already set in Jenkins config)
        SONARQUBE_SERVER = 'Sonarqube 9.9.7'  // This should match the name of your SonarQube server in Jenkins config
        SONARQUBE_SCANNER = 'Maven' // You can configure the tool globally in Jenkins if needed
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'refs/heads/master']],  // Use refs/heads for branch names
                    userRemoteConfigs: [[url: 'https://github.com/Pragya2028/groovy-pipeline-examples.git']]
                ])
            }
        }

        stage('Build') {
            steps {
                script {
                    // Example of a Maven build (adjust this if you're using another build tool like Gradle, etc.)
                    sh 'mvn clean install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube analysis using the SonarQube Scanner
                    withSonarQubeEnv(SONARQUBE_SERVER) {
                        sh "mvn sonar:sonar -Dsonar.projectKey=demoapp-project -Dsonar.sources=src"
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for SonarQube Quality Gate status
                    def qualityGate = waitForQualityGate()
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
    }

    post {
        success {
            echo 'SonarQube analysis completed successfully!'
        }
        failure {
            echo 'Build or SonarQube analysis failed!'
        }
    }
}
