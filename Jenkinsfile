#!/user/bin/env groovy

def COLOR_MAP = ['SUCCESS': 'good', 'FAILURE': 'danger', 'UNSTABLE': 'danger', 'ABORTED': 'danger']

node {
  try {

    // stage('Verifiying Filesystem') {
    //   wrap([$class: 'BuildUser']) {
    //     currentBuild.displayName = "#${env.BUILD_NUMBER} - ${BUILD_USER_ID} - ${SERVICE} - ${HOST}"
    //     sh "ansible-playbook -i ./inventories/hosts ./main.yml --tags 'filesystem' --extra-vars 'host=${HOST}'"
    //   }
    // }

    stage('download artifact') {
      withCredentials([usernamePassword(credentialsId: 'JFROG_CREDENTIALS', usernameVariable: 'JFROG_USERNAME', passwordVariable: 'JFROG_PASSWORD')]) {
        sh(script: "wget --user ${JFROG_USERNAME} --password ${JFROG_PASSWORD} ${ARTIFACT_URL} -O artifact.zip")
      }
    }

    stage('deploy artifact') {
      withCredentials([usernamePassword(credentialsId: 'SSH_CREDENTIALS', usernameVariable: 'SSH_USERNAME', passwordVariable: 'SSH_PASSWORD'), string(credentialsId: "${HOST}", variable: "PASSPHRASE")]) {
        sh "ansible-playbook -i ./inventories/hosts ./main.yml --tags '${COMPONENT}' --extra-vars 'ARTIFACT_URL=${ARTIFACT_URL} HOST=${HOST} REMOTE_PATH=${REMOTE_PATH} SSH_USERNAME=${SSH_USERNAME} SSH_PASSWORD=${SSH_PASSWORD}'"
      }
    } 

  } catch(Exception e) {
    currentBuild.result = "FAILURE"
    echo "Exception: ${e}"
  } finally {
    cleanWs()
  }
}
