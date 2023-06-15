node  {
    def app

    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    
    stage('Clone repository') {
            script{
                checkout scm
            }
        }

    stage('Build image') {
       app = docker.build("gitops-sample-app")
    }

    stage('Test image') {

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {

        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'), string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')]) { 
            sh 'echo $AWS_ACCESS_KEY_ID'
            sh 'echo $AWS_SECRET_ACCESS_KEY'
            sh 'echo ${BUILD_NUMBER}'
            sh 'echo $BUILD_NUMBER'
            sh 'aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 035296596762.dkr.ecr.ap-southeast-1.amazonaws.com'
            sh 'docker tag gitops-sample-app:latest 035296596762.dkr.ecr.ap-southeast-1.amazonaws.com/gitops-sample-app:${BUILD_NUMBER}'
            sh 'docker push 035296596762.dkr.ecr.ap-southeast-1.amazonaws.com/gitops-sample-app:${BUILD_NUMBER}'
        } 
        // withEnv (["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}","AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}","AWS_DEFAULT_REGION='ap-southeast-1'"]) {
        
        // }
        // docker.withRegistry('https://035296596762.dkr.ecr.ap-southeast-1.amazonaws.com', 'ecr:ap-southeast-1:aws-credentials') {
        //     app.push("${env.BUILD_NUMBER}")
        // }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
}

