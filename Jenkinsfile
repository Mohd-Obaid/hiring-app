pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_HUB_USERNAME = "mobaid15"
        DOCKER_HUB_PASSWORD = "Anon9542@"
        GITHUB_CREDENTIALS = credentials('Github_server')
    }

    stages {
        stage('Docker Build') {
            steps {
                sh "docker build . -t mobaid15/hiring-app:$BUILD_NUMBER"
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD}"
                sh "docker push mobaid15/hiring-app:${BUILD_NUMBER}"
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Github_server', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        sed -i "s/17/${BUILD_NUMBER}/g" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        git add .
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://${USERNAME}:${PASSWORD}@github.com/Mohd-Obaid/Hiring-app-argocd.git main
                        '''
                    }
                }
            }
        }
    }
}
