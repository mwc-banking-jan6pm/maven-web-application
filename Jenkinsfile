node{
def mavenHome = tool name: 'maven3.9.5'

echo "The job name is: ${env.JOB_NAME}"
echo "The jenkins home dir is: ${env.JENKINS_HOME}"
echo "The jenkins node name is: ${env.NODE_NAME}"
echo "The build number is: ${env.Build_NUMBER}"

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

stage('CheckoutCode'){
git branch: 'development', credentialsId: 'a2b3c888-b870-4463-b3cf-70bddb7adc01', url: 'https://github.com/mwc-banking-jan6pm/maven-web-application.git'
}

stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage('ExecuteSonarQubeReport'){
sh "${mavenHome}/bin/mvn sonar:sonar"    
}

stage('UploadArtifactIntoNexus'){
sh "${mavenHome}/bin/mvn clean deploy"    
}

stage('DeployAppIntoTomcat'){
sshagent(['32ac4bd7-11fc-4378-9cb5-38341eb75303']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.9.26:/opt/apache-tomcat-9.0.87/webapps/"
}
}

}
