pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'npm install'
                sh 'export NODE_OPTIONS=--openssl-legacy-provider && npm run build'
            }
        }
        stage('File Compression') {
            steps {
                echo '======File-Compression======'
                bat "zip -r docker-web-deploy.zip node_modules public package.json package-lock.json"
            }
        }
        stage('Upload to S3') {
            steps {
                echo '======Upload-to-S3======'
                bat "aws s3 cp docker-web-deploy.zip s3://elasticbeanstalk-ap-northeast-2-367976890732/app/"
            }
        }
        stage('Deploy'){
            steps {
                echo '======Deploy======'
                bat "aws elasticbeanstalk create-application-version --application-name docker-web-deploy --version-label docker-web-deploy-${BUILD_NUMBER} --source-bundle S3Bucket=\"elasticbeanstalk-ap-northeast-2-367976890732\",S3Key=\"docker-web-deploy.zip\""
                bat "aws elasticbeanstalk update-environment --environment-name Dockerwebdeploy-env --version-label docker-web-deploy-${BUILD_NUMBER}"
            }
        }
    }
}
// https://jeongyunlog.netlify.app/develop/devops/nextjs-jenkins-and-elb/