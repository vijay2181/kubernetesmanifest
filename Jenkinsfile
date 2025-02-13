pipeline {
    agent any

    parameters {
        string(name: 'DOCKERTAG', defaultValue: 'latest', description: 'Docker Image Tag')
    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Update GIT') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            sh "git config user.email vijay.kumar@gmail.com"
                            sh "git config user.name vijay2181"
                            sh "cat deployment.yaml"
                            sh "sed -i 's+vijay4devops/argocd-demo.*+vijay4devops/argocd-demo:${DOCKERTAG}+g' deployment.yaml"
                            sh "cat deployment.yaml"
                            sh "git add ."
                            sh "git commit -m 'Updated manifest: ${DOCKERTAG}'"
                            sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
                        }
                    }
                }
            }
        }
    }
}
