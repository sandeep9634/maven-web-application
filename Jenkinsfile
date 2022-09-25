node
{
def mavenHome = tool name: "Maven3.8.6"
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
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
  sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.233.192.131:/opt/apache-tomcat-9.0.65/webapps/"
}
}

}
