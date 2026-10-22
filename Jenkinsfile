
pipeline {
    agent any

    parameters {
        choice(
            name: 'BUILD_MODE',
            choices: ['full', 'test-only'],
            description: 'Select pipeline execution mode'
        )
    }

    stages {
        stage('Pull Latest Code') {
            steps {
                checkout scm
                echo 'Latest code pulled from GitHub'
            }
        }

        stage('Parallel Validation') {
            parallel {
                stage('Check Required Files') {
                    steps {
                        bat 'if not exist index.html exit /b 1'
                        bat 'if not exist Dockerfile exit /b 1'
                        bat 'if not exist deployment.yaml exit /b 1'
                        bat 'if not exist Jenkinsfile exit /b 1'
                        echo 'All required files exist'
                    }
                }

                stage('Test Dashboard') {
                    steps {
                        bat 'findstr /i /c:"Industrial Predictive Maintenance Dashboard" index.html'
                        bat 'findstr /i /c:"function render" index.html'
                        echo 'Dashboard checks passed'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            when {
                expression {
                    params.BUILD_MODE == 'full'
                }
            }
            steps {
                bat 'docker build -t industrial-maintenance:latest .'
                echo 'Docker image built successfully'
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                archiveArtifacts(
                    artifacts: 'index.html,Dockerfile,deployment.yaml,Jenkinsfile',
                    fingerprint: true
                )
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
