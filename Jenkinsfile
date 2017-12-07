#!groovy

def mailto = "h.babraa@sheffield.ac.uk"
def talend_home = "/opt/talend"

pipeline { //Declarative Pipeline will do checkout automatically
    agent any
    /*tools { 
	maven 'Maven' 
	jdk 'Java 8' 
    }*/

	
    stages {
		stage('Generate and Compile Sources') {
		    steps {
			echo 'Generate and Compile Sources..' ${env.WORKSPACE} 
			    // Apache Maven related side notes:
			    // --batch-mode : recommended in CI to inform maven to not run in interactive mode (less logs)
			    // -V : strongly recommended in CI, will display the JDK and Maven versions in use.
			    //      Very useful to be quickly sure the selected versions were the ones you think.
			    // -U : force maven to update snapshots each time (default : once an hour, makes no sense in CI).
			    // -e : Produce execution error messages
			    // -Dsurefire.useFile=false : useful in CI. Displays test errors in the logs directly (instead of
			    //                            having to crawl the workspace files to see the cause).
			    			//tool actual names taken from "Managing Jenkins" → "Global Tool Configuration"
	withEnv(["JAVA_HOME=${ tool 'Java 8' }", 
		 "PATH+MAVEN=${tool 'Maven'}/bin:${env.JAVA_HOME}/bin"]) {
			sh "mvn --batch-mode -X -V -U -e  -Dsurefire.useFile=false -f /opt/talend/jenkins/ci-builder-pom.xml --settings /opt/talend/studio/configuration/maven_user_settings.xml org.talend:ci.builder:6.4.1:local-generate"
		    }
	}
		}
		stage('Test Coverage') {
		    steps {
			echo 'Test Coverage..'
		    }
		}
		stage('Tests') {
		    steps {
			echo 'Tests..'
		    }
		}
		stage('Package and Publish') {
		    steps {
			echo 'Package and Publish..'
		    }
		}
		stage('Deploy') {
		    steps {
			echo 'Deploy..'
		    }
		}
		stage('Verify Deploy') {
		    steps {
			echo 'Verify Deploy..'
		    }
		}
		stage('Build') {
		    steps {
			script {
			    if(env.GIT_BRANCH == "origin/master") {
				input message: 'Are you sure you want to deploy? (Click "Proceed" to continue)'
			    }
			}
			sh '/usr/local/jenkins-tools/rbenv/shims/bundle install'
			sh '/usr/local/jenkins-tools/rbenv/bin/rbenv rehash'
		    }
		}
		//end stages
    }

    post {
        changed {
            script {
		    step([$class: 'Mailer',
			notifyEveryUnstableBuild: true,
			recipients: "$mailto",
			sendToIndividuals: true])
            }
        }
    }
}
