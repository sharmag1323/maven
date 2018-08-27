
node('sai') {
// Delete the workspace
//deleteDir()
stage('Retrieve source code') {
    checkout scm
    delivery = load 'repository.groovy'
    sh " cd $WORKSPACE;/bin/mkdir Build-${env.BUILD_NUMBER} "
    }
try {
    stage('Maven Build') {
      docker.image('maven:3.5-jdk-8-alpine').inside {
        sh "mvn clean package -Dbuild.number=${BUILD_NUMBER}"
        sh "/bin/mv -f $WORKSPACE/target/*.war $WORKSPACE/Build-${env.BUILD_NUMBER}/sai_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war"
 slackSend baseUrl: 'https://devopsguru-workspace.slack.com/services/hooks/jenkins-ci/', channel: 'general', color: 'red', message: 'hai maven job success', tokenCredentialId: 'jenkins-slack'

      }
    }
    
   stage('Deploy') {
        sh "/bin/cp -f $WORKSPACE/Build-${env.BUILD_NUMBER}/sai_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war /opt/apache-tomcat-7.0.90/webapps/sai.war"
        
       sh "/bin/rm -rf /opt/apache-tomcat-7.0.90/webapps/ROOT/"
       sh "/bin/mv /opt/apache-tomcat-7.0.90/webapps/sai /opt/apache-tomcat-7.0.90/webapps/ROOT/" 
   slackSend baseUrl: 'https://devopsguru-workspace.slack.com/services/hooks/jenkins-ci/', channel: 'general', color: 'red', message: 'maven job success........', tokenCredentialId: 'jenkins-slack' 
   }
  
   delivery.artifactory()
  }
  catch (e) {
      currentBuild.result = "FAILED"
      throw e
    }
}

