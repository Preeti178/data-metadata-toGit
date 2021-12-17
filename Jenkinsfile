#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    //def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "sfdx force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
			   //rmsg = bat returnStdout: true, script: "sfdx force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
				//metadata retrieve
				rmsg = bat returnStdout: true, script: "sfdx force:source:retrieve -u ${HUB_ORG} -x ./force-app/main/default//package.xml -w 10"
				// data export
				rmsg1 = bat returnStdout: true, script: "sfdx force:data:tree:export -q queryFile -d ./force-app/main/default/outputData -p -u ${HUB_ORG}"
				
				//rmsg2 = bat returnStdout: true, script: "sfdx force:package:create -n myPackage -t Unlocked -r /force-app/main/default"
				//bat'git archive --format=zip -o default.zip HEAD'
				//bat'git archive --format zip --output Jenkins/.jenkins/workspace/data-metadata-toGit_main/force-app/main/default.zip main'
				withCredentials([gitUsernamePassword(credentialsId: 'c7fff462-5d29-472d-abb0-b653de59d291', gitToolName: 'Default')]) {
				bat 'git config --global user.email "preeti.singh@metacube.com"'
				bat 'git config --global user.name "Preeti178"'
				bat 'git reset'
				bat 'git add -A force-app/main/default'
				bat 'git commit -m "push to git"'
		
 bat 'git push -f https://github.com/Preeti178/SFDX-projectFinal.git HEAD:main'
				
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
		 println(rmsg1)
		 //println(rmsg2)
        }
    }
}
