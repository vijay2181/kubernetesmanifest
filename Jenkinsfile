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
                                    //def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                                    sh "git config user.email vijay.kumar@gmail.com"
                                    sh "git config user.name vijay2181"
                                    //sh "git switch master"
                                    sh "cat deployment.yaml"
                                    sh "sed -i 's+vijay4devops/argocd-demo.*+vijay4devops/argocd-demo:${DOCKERTAG}+g' deployment.yaml"
                                    sh "cat deployment.yaml"
                                    sh "git add ."
                                    sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/KubernetesManifest.git HEAD:main"
                       }
                  }
                }
            }
        }
    }
}
