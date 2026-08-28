pipeline {
    agent any

    stages {
        //Git checkout ofr Deployment
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/Bjrules/Capstone-DotNET-Mongo-CD-main.git'
            }
        }
        
        stage('Deploy To Kubernetes') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'bnj-cluster', contextName: '', credentialsId: 'K8s-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://95C845321242AE5D617824C874922E95.gr7.us-east-1.eks.amazonaws.com') {
                        sh 'kubectl apply -f Manifest/manifest.yaml -n webapps'
                        sh 'kubectl apply -f Manifest/ci.yaml'
                        sh 'kubectl apply -f Manifest/ingress.yaml -n webapps'
                        sleep 30
                    }
                }
            }
        }
        
         stage('Verify The Deployment') {
            steps {
                script {
                    withKubeConfig(caCertificate: '', clusterName: 'bnj-cluster', contextName: '', credentialsId: 'K8s-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://95C845321242AE5D617824C874922E95.gr7.us-east-1.eks.amazonaws.com') {
                        sh 'kubectl get pods -n webapps'
                        sh 'kubectl get svc -n webapps'
                        sh 'kubectl get ingress -n webapps'
                        sleep 30
                    }
                }
            }
        }
        
    }
    post {
    always {
        script {
            def jobName = env.JOB_NAME
            def buildNumber = env.BUILD_NUMBER
            def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
            def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'

            def body = """
                <html>
                <body>
                <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                <h2>${jobName} - Build ${buildNumber}</h2>
                <div style="background-color: ${bannerColor}; padding: 10px;">
                <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                </div>
                <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                </div>
                </body>
                </html>
            """

            emailext (
                subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                body: body,
                to: 'justbj@live.com',
                from: 'rulesxx@gmail.com',
                replyTo: 'rulesxx@gmail.com',
                mimeType: 'text/html',
               
            )
        }
    }
}
}


