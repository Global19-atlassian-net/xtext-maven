node {
	properties([
		[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '15']]
			, pipelineTriggers([upstream(threshold: 'SUCCESS', upstreamProjects: 'xtext-extras/milestone_2.18.0.Beta')])
	])
	
	stage('Checkout') {
		checkout scm
		
		dir('build') { deleteDir() }
		dir('.m2/repository/org/eclipse/xtext') { deleteDir() }
		dir('.m2/repository/org/eclipse/xtend') { deleteDir() }
	}
	
	stage('Maven Build') {
		def workspace = pwd()
		def mvnHome = tool 'M3'
		env.M2_HOME = "${mvnHome}"
		sh "${mvnHome}/bin/mvn --batch-mode --update-snapshots -fae -PuseJenkinsSnapshots -DJENKINS_URL=$JENKINS_URL -Dmaven.test.failure.ignore=true -Dmaven.repo.local=${workspace}/.m2/repository -f org.eclipse.xtext.maven.parent/pom.xml -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=warn clean deploy"
		step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/*.xml'])
	}
	
	archive 'build/**'
}
