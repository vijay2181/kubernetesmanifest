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
                            withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_TOKEN', usernameVariable: 'GIT_USERNAME')]) {
                                
                                sh 'git config user.email "vijay.kumar@gmail.com"'
                                sh 'git config user.name "vijay2181"'
            
                                sh 'git checkout ${BRANCH}'
                                sh 'git pull origin ${BRANCH}'
            
                                // Update deployment.yaml with the new tag
                                sh 'sed -i "s+vijay4devops/argocd-demo:.*+vijay4devops/argocd-demo:${DOCKERTAG}+g" deployment.yaml'
            
                                // Check if there are actual changes before committing
                                def changes = sh(script: 'git diff --name-only', returnStdout: true).trim()
            
                                if (changes) {
                                    echo "✅ Changes detected, committing..."
                                    sh 'git add deployment.yaml'
                                    sh 'git commit -m "Updated manifest: ${DOCKERTAG}"'
                                    
                                    // ✅ Use PAT for secure authentication
                                    sh '''
                                    git remote set-url origin https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/vijay2181/kubernetesmanifest.git
                                    git push origin ${BRANCH}
                                    '''
                                } else {
                                    echo "⚠️ No changes detected in deployment.yaml, skipping commit and push."
                                }
                            }
                        }
                    }
                }
            }
      }
}
