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
                sh 'npm run build'
            }
        }
        stage('File Compression') {
            steps {
                echo '======File-Compression======'
                script{
                    def packageJson = readJSON file: "package.json"
                    VERSION = packageJson.version
                }
                bat "zip -r docker-web-deploy-${VERSION}.zip node_modules public package.json package-lock.json"
            }
        }
        stage('Upload to S3') {
            steps {
                echo '======Upload-to-S3======'
                bat "aws s3 cp docker-web-deploy-${VERSION}.zip s3://elasticbeanstalk-ap-northeast-2-367976890732/app/"
            }
        }
        stage('Deploy'){
            steps {
                echo '======Deploy======'
                bat "aws elasticbeanstalk create-application-version --application-name docker-web-deploy --version-label docker-web-deploy-${BUILD_NUMBER} --source-bundle S3Bucket=\"elasticbeanstalk-ap-northeast-2-367976890732\",S3Key=\"docker-web-deploy-${VERSION}.zip\""
                bat "aws elasticbeanstalk update-environment --environment-name Dockerwebdeploy-env --version-label docker-web-deploy-${BUILD_NUMBER}"
            }
        }
    }
}
// https://jeongyunlog.netlify.app/develop/devops/nextjs-jenkins-and-elb/