podTemplate(
containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:alpine'),
    containerTemplate(name: 'maven', image: 'maven:3.6.3-jdk-11', ttyEnabled: true, command: 'cat')
  ],
volumes: [secretVolume(secretName: 'aws-creds', mountPath: '/root/.aws')]) {
  node(POD_LABEL) {
    stage('Install ECR Helper') {
      container('maven') {
        sh 'apt update && apt install amazon-ecr-credential-helper -y'
      }
    }
    stage('Build a Maven project') {
      checkout scm
      container('maven') {
        sh 'mvn -B clean package'
      }
    }
    stage('Build Integration Tests') {
      container('maven') {
        sh 'mvn -B  verify'
      }
    }
    stage('Build Docker Jib ') {
      container('maven') {
          sh 'mvn -B com.google.cloud.tools:jib-maven-plugin:1.6.1:build'
      }
    }

  }
}
