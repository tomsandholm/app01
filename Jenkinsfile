// vi:set nu ai ap aw smd showmatch tabstop=4 shiftwidth=4: 

pipeline {
  agent any
  options {
    timestamps();
  }

  environment {
    CAUSE = "${currentBuild.getBuildCauses()[0].shortDescription}"
  }

  stages {

    stage('checkout') {
      steps {
        checkout scm
      }
    }

    stage('setup') {
      steps {
        sh """
		  sudo apt-get install -y autoconf automake libtool checkinstall
          autoreconf --verbose --install --force
          ./configure
        """
      }
    }

    stage('build') {
      steps {
        sh """
          make all
          make dist
          make package
        """
      }
    }

	stage('check parent') {
	  steps {
		  echo "Build caused by ${env.CAUSE}"
      }	
	}
  }
  post {
    always {
      archiveArtifacts artifacts: 'app*.deb', onlyIfSuccessful: true
      step([$class: 'WsCleanup'])
    }
  }
}
