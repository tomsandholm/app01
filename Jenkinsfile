// vi:set nu ai ap aw smd showmatch tabstop=4 shiftwidth=4: 

pipeline {
  agent any
  options {
    timestamps();
  }

  environment {
    CAUSE = "${currentBuild.getBuildCauses()[0].shortDescription}"
	BUILD_BRANCH = "release/R5.0.x"
  }

  stages {

    stage('checkout') {
      steps {
        checkout scm
		buildDescription 'this is a test'
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
		  echo "BUILD_BRANCH: ${env.BUILD_BRANCH}"
		  echo "BUILD_BRANCH: ${BUILD_BRANCH}"
		  script {
		    sh (script: "echo ${env.BUILD_BRANCH}", label: "env BUILD_BRANCH")
		    sh (script: "echo ${BUILD_BRANCH}", label: "BUILD_BRANCH")
          }
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
