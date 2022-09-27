node
{
def mavenHome = tool name: "Maven3.8.6"
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
try {
notifyBuild('STARTED')
stage('CheckoutCode'){
git credentialsId: '38db5186-845e-41ab-849c-66af359b6f34', url: 'https://github.com/sandeep9634/maven-web-application.git'
}
stage('build'){
sh "${mavenHome}/bin/mvn clean package"
}
stage('executesonarqubereport'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}
stage('uploadtonexus'){
sh "${mavenHome}/bin/mvn deploy"
}
stage('deploytotomcat'){
sshagent(['f4a68dd4-aaa4-4576-8a7b-5f7454508c8a']) {
  sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.0.186:/opt/apache-tomcat-9.0.65/webapps/"
}
}
}
catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}


def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
