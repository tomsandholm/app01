// vi:set nu ai ap aw smd showmatch tabstop=4 shiftwidth=4: 

pipeline {
  agent any
  options {
    timestamps();
    copyArtifactPermission('toprepo');
  }

  environment {
    CAUSE = "${currentBuild.getBuildCauses()[0].shortDescription}"
  }

  parameters {
    string (
        description: 'Tag Override Value',
        name: 'tagovr',
        defaultValue: ''
    )
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
    
    stage('tag') {
        steps {
            script {
                if ( params.tagovr != '' ) {
                    def TAGSTRING = "${params.tagovr}"
                    echo "##### Tag OVERRIDE is applied"
                    echo "##### Tag: $TAGSTRING"
                    sh "make -e STAG=$TAGSTRING stag"
                } else {
                    sh 'make stag'
                }
            }
        }
    }

    stage('tag push') {
      steps {
        sh """
          make stagpush
        """
      }
    }
  }
  post {
    always {
      archiveArtifacts artifacts: 'sub*.deb', onlyIfSuccessful: true
      step([$class: 'WsCleanup'])
    }
  }
}
