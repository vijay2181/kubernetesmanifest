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
            script {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh "git config user.email vijay.kumar@gmail.com"
                        sh "git config user.name vijay2181"
                        
                        // Ensure we're on the correct branch
                        sh "git checkout main"
                        sh "git pull origin main"
        
                        // Debugging: Check file before change
                        sh "cat deployment.yaml"
        
                        // Modify the file
                        sh "sed -i 's+vijay4devops/argocd-demo.*+vijay4devops/argocd-demo:${DOCKERTAG}+g' deployment.yaml"
        
                        // Debugging: Check file after change
                        sh "cat deployment.yaml"
        
                        // Ensure Git detects changes
                        sh "git status"
        
                        // Force commit if needed
                        sh "git add -A"
                        sh "git commit -m 'Updated manifest: ${DOCKERTAG}' || echo 'No changes to commit'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main"
                    }
                }
            }
        }

    }
}
