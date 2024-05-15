pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage('doc') {
      steps {
        sh 'mvn javadoc:jar --fail-never'
      }
    }
    stage('pmd') {
      steps {
        sh 'mvn pmd:pmd'
      }
    }
    stage('Test'){
      steps {
        sh 'mvn -Dtest=TestCss test'
      }
      
    }
  }  
  post {
    always {
      archiveArtifacts artifacts: '**/target/site/**', fingerprint: true
      archiveArtifacts artifacts: '**/target/**/*.jar', fingerprint: true
      archiveArtifacts artifacts: '**/target/**/*.war', fingerprint: true
      archiveArtifacts artifacts: '**/target/surefire-reports/**', fingerprint: true
    }  
  }
}
