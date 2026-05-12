pipeline {

    agent any

    tools {
        jdk 'java'
        maven 'maven'
    }

    environment {
        SCANNER_HOME = tool 'sonar'
    }

    stages {

        stage('Clone Code') {

            steps {

                git branch: 'main',
                url: 'https://github.com/Pratap4004/BoardgameListingWebApp.git',
                credentialsId: 'git-cred'

            }

        }

        stage('Build Maven') {

            steps {

                withMaven(
                    maven: 'maven',
                    globalMavenSettingsConfig: 'global-settings'
                ) {

                    sh 'mvn clean package'

                }

            }

        }

        stage('SonarQube Analysis') {

            steps {

                withSonarQubeEnv('sonar') {

                    sh """
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=myapp \
                    -Dsonar.projectKey=myapp \
                    -Dsonar.java.binaries=target
                    """

                }

            }

        }

        stage('Trivy File Scan') {

            steps {

                sh 'trivy fs .'

            }

        }

        stage('Upload To Nexus') {

            steps {

                withMaven(
                    maven: 'maven',
                    globalMavenSettingsConfig: 'global-settings'
                ) {

                    sh 'mvn deploy'

                }

            }

        }

        stage('Docker Build') {

            steps {

                sh 'docker build -t prathap4004/myapp:v1 .'

            }

        }

        stage('Trivy Image Scan') {

            steps {

                sh 'trivy image prathap4004/myapp:v1'

            }

        }

        stage('Docker Push') {

            steps {

                withDockerRegistry(
                    credentialsId: 'docker-cred',
                    url: 'https://index.docker.io/v1/'
                ) {

                    sh 'docker push prathap4004/myapp:v1'

                }

            }

        }

        stage('Deploy To Kubernetes') {

            steps {

                sh 'kubectl apply -f deployment.yaml'

                sh 'kubectl apply -f service.yaml'

            }

        }

        stage('Verify Deployment') {

            steps {

                sh 'kubectl get pods'

                sh 'kubectl get svc'

            }

        }

    }

}
