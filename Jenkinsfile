pipeline {
    agent {
        kubernetes {
            cloud 'demo-prod-k8s-us-east'
            yamlFile k8s-agent.yaml
            defaultContainer 'node-18'
        }
    }
    tools {
        nodejs 'nodejs-22-6-0'
    }

    environment {
        MONGO_URI = "mongoose.connect('mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGODB_DB_CREDS = credentials('mongo-db-credentials')
        MONGO_USERNAME = credentials('mongo-db-username')
        MONGO_PASS = credentials('mongo-db-password')
    }

    options {
        disableResume()
        disableConcurrentBuilds abortPrevious: true
    }

    stages {
        stage('Installing Dependencies') {
            options { timestamps()}
            steps {
                sh 'node -v'
                sh 'npm install --no-audit'
            }
        }

        stage('Deps Scanning') {
            parallel {
                stage ('NPM dep audit') {
                    steps {
                        sh '''
                            node -v'
                            npm audit --audit-level=critical'
                            echo $?
                        '''
                    }
                }
            }
        stage('Unit testing') {
            parallel {
                stage('Nodejs 18') {
                    options { timestamps()}
                    steps {
                        sh 'node -v'
                        sh 'npm test'
                    }
                }
                stage('Nodejs 19'){
                    options { timestamps()}
                    steps {
                        container('node-19')
                            sh 'sleep 10s'
                            sh 'node -v'
                            sh 'npm test'
                    }
                }

                stage('Nodejs 20'){
                    agent {
                        docker {
                            image 'node:20-alpine'
                        }
                    options { retry(2) }
                    steps {
                        sh 'sleep 10s'
                        sh 'node -v'
                        sh 'npm test'
                    }
                }
            }
        }
    }
}