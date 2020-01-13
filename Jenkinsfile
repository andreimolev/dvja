pipeline {
  agent any

  tools {
    maven "apache-maven-3.6.3"
  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/ajlanghorn/dvja.git'
        sh "mvn clean package"
      }
    }
    stage('Check dependencies') {
      steps {
        dependencyCheck additionalArguments: '', odcInstallation: 'Dependency-Check'
        dependencyCheckPublisher failedTotalCritical: 50, failedTotalHigh: 100, failedTotalLow: 200, failedTotalMedium: 150, pattern: '', unstableTotalCritical: 50, unstableTotalHigh: 100, unstableTotalLow: 200, unstableTotalMedium: 150
      }
    }
    stage('Scan for vulnerabilities') {
      steps {
        sh 'java -jar target/dvja-*.war && zap-cli quick-scan --self-contained --spider -r http://127.0.0.1 && zap-cli report -o zap-report.html -f html'
        archiveArtifacts artifacts: 'zap-report.html', fingerprint: true
      }
    }
    stage('Publish to S3') {
      steps {
        sh "aws s3 cp /var/lib/jenkins/workspace/dvja/target/dvja-1.0-SNAPSHOT.war s3://cicd3-buildartifacts-ykz4ov5yhz80/dvja-1.0-SNAPSHOT.war"
      }
    }
    stage('Tidy up') {
      steps {
        cleanWs()
      }
    }
  }
}
