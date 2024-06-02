pipeline {
    

    agent any

    stages {
        
        stage('Checkout Source') {
            steps {
                git branch: 'main', url: 'https://github.com/taqiyeddinedj/devsecopsManifests.git'
            }
        }

        stage('Update Git Repository') {
            steps {
                // Check out the Git repository
                git branch: 'main', url: 'https://github.com/taqiyeddinedj/devsecopsManifests.git'

                // Replace the image tag in the Kubernetes manifest files 
                sh "sed -i 's|taqiyeddinedj/devsecops:.*|taqiyeddinedj/devsecops:webapp-${DOCKERTAG}|g' deploy.yaml"

                // Add, commit, and push the changes to the Git repository
                withCredentials([usernamePassword(credentialsId: 'github-token', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh """
                        git config --global user.email "djouani.taqiyeddine@gmail.com"
                        git config --global user.name "${GIT_USERNAME}"
                        git add manifests/deploy.yaml
                        git commit -m "Update image tag to ${DOCKERTAG}"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/taqiyeddinedj/devsecopsManifests.git main
                    """
                }

            }
        }



        // i think that we should delete this section of ArgoCD, cz we are forcing argocd to fetching the manifests files from github
        stage('Deploy to kubernetes using ArgoCD'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: 'default', contextName: '', credentialsId: 'k8s-cred', namespace: 'devsecops', restrictKubeConfigAccess: false, serverUrl: 'https://10.231.10.16:6443') {
                        //sh "sed -i 's|taqiyeddinedj/devsecops:webapp-1.0|taqiyeddinedj/devsecops:${BUILD_NUMBER}|g' manifests/deploy.yaml"
                        sh "kubectl apply -f application.yaml"
                }
            }
        }

        
    }
}