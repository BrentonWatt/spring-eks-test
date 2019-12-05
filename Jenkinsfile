podTemplate(
containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:alpine'),
    containerTemplate(name: 'maven', image: 'maven:3.6.3-jdk-11', ttyEnabled: true, command: 'cat')
  ],
volumes: [secretVolume(secretName: 'aws-creds', mountPath: '/root/.aws'),
          configMapVolume(configMapName: 'aws-config', mountPath: '/root/.aws'),
          configMapVolume(configMapName: 'ecr-dockerconfig', mountPath: '/root/.docker')
          ]) {
  node(POD_LABEL) {
    stage('Install ECR Helper') {
      container('maven') {
        sh 'curl -L https://amazon-ecr-credential-helper-releases.s3.us-east-2.amazonaws.com/0.3.1/linux-amd64/docker-credential-ecr-login -o /usr/bin/docker-credential-ecr-login'
        sh 'chmod a+x /usr/bin/docker-credential-ecr-login'
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
          sh 'AWS_PROFILE=ecr-user aws iam get-user'
          sh 'AWS_PROFILE=ecr-user mvn -B com.google.cloud.tools:jib-maven-plugin:1.6.1:build'
      }
    }

  }
}
