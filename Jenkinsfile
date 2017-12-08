#!groovy

def mailto = "h.babraa@sheffield.ac.uk"

/*

Apache Maven related side notes:
--batch-mode : recommended in CI to inform maven to not run in interactive mode (less logs)
-V : strongly recommended in CI, will display the JDK and Maven versions in use. Very useful to be quickly sure the selected versions were the ones you think.
-U : force maven to update snapshots each time (default : once an hour, makes no sense in CI).
-e : Produce execution error messages
-Dsurefire.useFile=false : useful in CI. Displays test errors in the logs directly (instead of having to crawl the workspace files to see the cause).
-f pass in pom
--settings override local settings

*/


pipeline { //Declarative Pipeline will do checkout automatically
    agent any
    environment {
        TALEND_HOME = "/opt/talend"
        MAVEN_CMD_OPTS =" --batch-mode -V -U -e" +
                " -Dsurefire.useFile=false" +
                " --settings ${TALEND_HOME}/studio/configuration/maven_user_settings.xml"
    }
    tools {
        //Tool actual names taken from "Managing Jenkins" → "Global Tool Configuration"
        maven "Maven"
        jdk "Java 8"
    }
    stages {
        stage("Generate Sources") {
            steps {
                sh "mvn ${MAVEN_CMD_OPTS}" + 
		" -f ${TALEND_HOME}/jenkins/ci-builder-pom.xml" + // pom used to run ci-builder to create maven java project with src and pom
		" -Ddeploy.version=02-SNAPSHOT" + // version to pass through through ci builder to add to created pom
		" clean org.talend:ci.builder:6.4.1:local-generate" //goal to generate sources using above pom
            }
        }
        stage("Build and Test") {
            steps {
                sh "mvn ${MAVEN_CMD_OPTS}" + 
		" -f ${WORKSPACE}/projectSources/pom.xml" + // project pom created by ci-builder
		" test" //goal to compile sources and run tests using above pom
            }
        }
        stage("Package and Publish") {
            steps {
                sh "mvn ${MAVEN_CMD_OPTS}" + 
		" -DaltDeploymentRepository=tac::default::http://tal-dev-admin.shef.ac.uk:8081/nexus/content/repositories/snapshots/" + //refernce to nexus repo - also seems to require a default url!
		" -f ${WORKSPACE}/projectSources/pom.xml" +  // project pom created by ci-builder
		" deploy" //goal to compile deploy using the above pom
            }
        }
        stage("Deploy to Runtime") {
            steps {
                echo 'Deploy..'
            }
        }
        stage("Verify Deploy to Runtime") {
            steps {
                echo 'Deploy..'
            }
        }
        stage("Build") {
            steps {
                script {
                    if(env.GIT_BRANCH == "origin/master") {
                        input message: "Are you sure you want to deploy? (Click "Proceed" to continue)"
                    }
                }
                //sh "/usr/local/jenkins-tools/rbenv/shims/bundle install"
                //sh "/usr/local/jenkins-tools/rbenv/bin/rbenv rehash"
            }
        }
    stage ('success'){
            steps {
                script {
                    currentBuild.result = 'SUCCESS'
                }
            }
        }
    }

    post {
        failure {
            script {
                currentBuild.result = 'FAILURE'
            }
        }

        always {
            step([$class: 'Mailer',
                notifyEveryUnstableBuild: true,
			    recipients: "$mailto",
                sendToIndividuals: true])
        }
    }
}
