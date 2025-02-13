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
                            
                            sh "git config user.email 'vijay.kumar@gmail.com'"
                            sh "git config user.name 'vijay2181'"

                            // Ensure we are on the correct branch and pull latest changes
                            sh "git checkout ${BRANCH}"
                            sh "git pull origin ${BRANCH}"

                            // Debugging: Print file before modification
                            sh "echo 'Before Update:'"
                            sh "cat deployment.yaml"

                            // Modify deployment file with the new Docker tag
                            sh "sed -i 's+vijay4devops/argocd-demo:.*+vijay4devops/argocd-demo:${DOCKERTAG}+g' deployment.yaml"

                            // Debugging: Print file after modification
                            sh "echo 'After Update:'"
                            sh "cat deployment.yaml"

                            // Ensure Git detects changes
                            sh "git status"

                            // Add changes and commit
                            sh "git add -A"
                            sh "git commit -m 'Updated manifest: ${DOCKERTAG}' || echo 'No changes to commit'"

                            // ✅ Secure authentication with `git credential.helper`
                            sh """
                            git config credential.helper 'cache --timeout=10'
                            echo 'https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com' > ~/.git-credentials
                            git push origin ${BRANCH}
                            rm -f ~/.git-credentials
                            """

                        }
                    }
                }
            }
        }
    }
}
