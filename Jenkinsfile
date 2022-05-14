// vi:set nu ai ap aw smd showmatch tabstop=4 shiftwidth=4: 

@Library('jenkins-shared-library') _

properties([
  parameters([
    [
      $class: 'ChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      name: 'Environment',
      script: [
        $class: 'ScriptlerScript',
        scriptlerScriptId:'Environments.groovy'
      ]
    ],
    [
      $class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      name: 'Host',
      referencedParameters: 'Environment',
      script: [
        $class: 'ScriptlerScript',
        scriptlerScriptId:'HostsInEnv.groovy',
        parameters: [
          [name:'Environment', value: '$Environment']
        ]
      ]
   ]
 ])
])

def sayHello(String name = 'human') {
  echo "Hello, ${name}"
}

pipeline {
  agent { label 'jenkins01' }
  options {
    timestamps();
  }

  environment {
    CAUSE = "${currentBuild.getBuildCauses()[0].shortDescription}"
	GIT_REPO_NAME = env.GIT_URL.replaceFirst(/^.*\/([^\/]+?).git$/, '$1')
	GIT_BRANCH_NAME = "${GIT_BRANCH.split('/').size() >1 ? GIT_BRANCH.split('/')[1..-1].join('/') : GIT_BRANCH}"
  }

  stages {
    stage('build'){
	  steps {
	    echo "${params.Environment}"
		echo "${params.Host}"
	  }
	}

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
		  echo 'use single quotes Build caused by ${env.CAUSE}'
		  sayHello 'Thomas'
		  helloWorld 'this is from the jenkins-shared-library'
		  echo "call printargs.groovy"
		  script {
		  artifacts = ["one","two","three","four","five"]
		  printargs artifacts as String[]
		  }
		  sh '''
		    x=$(date)
			echo $x
			echo ${GIT_REPO_NAME}
			'''
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
