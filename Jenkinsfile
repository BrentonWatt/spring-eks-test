podTemplate(
containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:alpine'),
    containerTemplate(name: 'maven', image: 'maven:3.6.3-jdk-11', ttyEnabled: true, command: 'cat')
  ]) {
  node(POD_LABEL) {
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
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                          usernameVariable: 'USER',
                                          passwordVariable: 'PASS')]) {
          sh 'mvn -B com.google.cloud.tools:jib-maven-plugin:1.6.1:build -Djib.to.auth.username=$USER -Djib.to.auth.password=$PASS'
        }
      }
    }

  }
}
