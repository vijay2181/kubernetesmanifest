pipeline {
    agent any

    parameters {
        string(name: 'DOCKERTAG', defaultValue: 'latest', description: 'Docker tag for deployment')
    }

    environment {
        GIT_REPO = 'https://github.com/vijay2181/kubernetesmanifest.git'
        BRANCH = 'main'
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: "*/${BRANCH}"]],
                    userRemoteConfigs: [[url: "${GIT_REPO}"]]
                ])
            }
        }

        stage('Update GIT') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            
                            sh 'git config user.email "vijay.kumar@gmail.com"'
                            sh 'git config user.name "vijay2181"'

                            sh 'git checkout ${BRANCH}'
                            sh 'git pull origin ${BRANCH}'

                            sh 'sed -i "s+vijay4devops/argocd-demo:.*+vijay4devops/argocd-demo:${DOCKERTAG}+g" deployment.yaml'

                            sh 'git add -A'
                            sh 'git commit -m "Updated manifest: ${DOCKERTAG}" || echo "No changes to commit"'

                            // ✅ Securely store credentials in Git and push
                            sh '''
                            git config credential.helper store
                            echo "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com" > ~/.git-credentials
                            git push origin ${BRANCH}
                            rm -f ~/.git-credentials
                            '''

                        }
                    }
                }
            }
        }
    }
}
