pipeline {
    agent any

    parameters {
        string(name: 'DOCKERTAG', defaultValue: '', description: 'Tag of the Docker image')
    }

    stages {
        stage('Checkout Source') {
            steps {
                git branch: 'main', url: 'https://github.com/taqiyeddinedj/devsecopsManifests.git'
            }
        }

        stage('Update Git Repository') {
            steps {
                sh "sed -i 's|taqiyeddinedj/devsecops:.*|taqiyeddinedj/devsecops:webapp-${params.DOCKERTAG}|g' manifests/deploy.yaml"

                withCredentials([usernamePassword(credentialsId: 'github-token', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh """
                        git config --global user.email "djouani.taqiyeddine@gmail.com"
                        git config --global user.name "${GIT_USERNAME}"
                        git add manifests/deploy.yaml
                        git commit -m "Update image tag to ${params.DOCKERTAG}"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/taqiyeddinedj/devsecopsManifests.git main
                    """
                }
            }
        }

        stage('Deploy to Kubernetes using ArgoCD') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'default', contextName: '', credentialsId: 'k8s-cred', namespace: 'devsecops', restrictKubeConfigAccess: false, serverUrl: 'https://10.231.10.16:6443') {
                    sh "kubectl apply -f application.yaml"
                }
            }
        }
    }
}
