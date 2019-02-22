pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    label "jenkins-maven"
  }
  environment {
    DEPLOY_NAMESPACE = "jx-staging"
  }
  stages {
    stage('Validate Environment') {
      steps {
        container('maven') {
          dir('env') {
            sh 'jx step helm build'
          }
        }
      }
    }
    stage('Update Environment') {
      when {
        branch 'master'
      }
      steps {
        container('maven') {
          dir('env') {
            sh 'wget https://github.com/mozilla/sops/releases/download/3.2.0/sops-3.2.0-1.x86_64.rpm && yum install -y sops-3.2.0-1.x86_64.rpm && rm sops-3.2.0-1.x86_64.rpm'
            sh 'sops -d jerry2-env.enc > jerry2-env && {kubectl delete secret --namespace jx-staging jerry2-env || true} && kubectl create secret generic jerry2-env --from-env-file=jerry2-env --namespace jx-staging'
            sh 'jx step helm apply'
          }
        }
      }
    }
  }
}
