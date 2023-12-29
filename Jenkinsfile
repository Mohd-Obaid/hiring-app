pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_HUB_USERNAME = "mobaid15"
        DOCKER_HUB_PASSWORD = "Anon9542@"
        GITHUB_PAT_CREDENTIAL = credentials('GitHub_TOKEN') 
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

        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/Mohd-Obaid/Hiring-app-argocd.git', credentialsId: 'GitHub_PAT'
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'GitHub_PAT', variable: 'GITHUB_PAT')]) {
                        sh '''
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        sed -i "s/17/${BUILD_NUMBER}/g" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        git add .
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://${GitHub_TOKEN}@github.com/Mohd-Obaid/Hiring-app-argocd.git main
                        '''
                    }
                }
            }
        }
    }
}
