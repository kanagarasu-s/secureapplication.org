```
pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'jdk17'
    }

    environment {
        APP_NAME      = "SMARTCRPAPI"
        DEPLOY_USER   = "root"
        DEPLOY_SERVER = "192.168.116.54"
        DEPLOY_DIR    = "/opt/apps/smartcrpapi"
        SSH_KEY_ID    = "deploy-ssh-key"
        BUILD_JAR     = "smartcrp-api-0.0.1-SNAPSHOT.jar"
        DEPLOY_JAR    = "smartcrp-api-0.0.1.jar"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'gitlab-creds',
                    url: 'http://gitlab.ctleng.com/CTL/smartcrpapi.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: "target/${BUILD_JAR}",
                                  fingerprint: true
            }
        }

        stage('SSH Test') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: SSH_KEY_ID,
                        keyFileVariable: 'SSH_KEY'
                    )
                ]) {
                    sh '''
                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    ${DEPLOY_USER}@${DEPLOY_SERVER} \
                    "hostname && whoami"
                    '''
                }
            }
        }

        stage('Deploy JAR') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: SSH_KEY_ID,
                        keyFileVariable: 'SSH_KEY'
                    )
                ]) {
                    sh '''
                    # Create deploy directory
                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    ${DEPLOY_USER}@${DEPLOY_SERVER} \
                    "mkdir -p ${DEPLOY_DIR}"

                    # Copy JAR to server
                    scp -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    target/${BUILD_JAR} \
                    ${DEPLOY_USER}@${DEPLOY_SERVER}:${DEPLOY_DIR}/

                    # Rename JAR on server
                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    ${DEPLOY_USER}@${DEPLOY_SERVER} \
                    "cd ${DEPLOY_DIR} && mv ${BUILD_JAR} ${DEPLOY_JAR}"
                    '''
                }
            }
        }
    }
}


```
