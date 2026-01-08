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
        BACKUP_JAR    = "smartcrp-api-backup.jar"
    }

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: env.gitlabSourceBranch ?: 'main',
                    credentialsId: 'gitlab-creds',
                    url: 'http://gitlab.ctleng.com/CTL/smartcrpapi.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: "target/${BUILD_JAR}", fingerprint: true
            }
        }

        /* 🚫 DEPLOY ONLY ON MAIN (NO MERGE REQUEST) */
        stage('Backup Existing JAR') {
            when {
                allOf {
                    branch 'main'
                    expression { env.gitlabMergeRequestIid == null }
                }
            }
            steps {
                withCredentials([
                    sshUserPrivateKey(credentialsId: SSH_KEY_ID, keyFileVariable: 'SSH_KEY')
                ]) {
                    sh '''
                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    ${DEPLOY_USER}@${DEPLOY_SERVER} \
                    "cd ${DEPLOY_DIR} && \
                     [ -f ${DEPLOY_JAR} ] && cp ${DEPLOY_JAR} ${BACKUP_JAR} || true"
                    '''
                }
            }
        }

        stage('Deploy JAR') {
            when {
                allOf {
                    branch 'main'
                    expression { env.gitlabMergeRequestIid == null }
                }
            }
            steps {
                withCredentials([
                    sshUserPrivateKey(credentialsId: SSH_KEY_ID, keyFileVariable: 'SSH_KEY')
                ]) {
                    sh '''
                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    ${DEPLOY_USER}@${DEPLOY_SERVER} "mkdir -p ${DEPLOY_DIR}"

                    scp -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    target/${BUILD_JAR} \
                    ${DEPLOY_USER}@${DEPLOY_SERVER}:${DEPLOY_DIR}/

                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    ${DEPLOY_USER}@${DEPLOY_SERVER} \
                    "cd ${DEPLOY_DIR} && mv ${BUILD_JAR} ${DEPLOY_JAR}"
                    '''
                }
            }
        }

        stage('Restart Application') {
            when {
                allOf {
                    branch 'main'
                    expression { env.gitlabMergeRequestIid == null }
                }
            }
            steps {
                withCredentials([
                    sshUserPrivateKey(credentialsId: SSH_KEY_ID, keyFileVariable: 'SSH_KEY')
                ]) {
                    sh '''
                    ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no \
                    ${DEPLOY_USER}@${DEPLOY_SERVER} <<EOF
                    pkill -f ${DEPLOY_JAR} || true
                    nohup java -jar ${DEPLOY_DIR}/${DEPLOY_JAR} > app.log 2>&1 &
                    sleep 10
EOF
                    '''
                }
            }
        }
    }
}

```
