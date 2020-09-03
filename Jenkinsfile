GROOVY SCRIPT

#!groovy

import groovy.json.JsonSlurperClassic

node { //def BUILD_NUMBER=env.BUILD_NUMBER

//def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"

def SFDC_USERNAME

def HUB_ORG='***********'

def SFDC_HOST = 'https://test.salesforce.com'

def JWT_KEY_CRED_ID = '********'

def CONNECTED_APP_CONSUMER_KEY='********'

println 'KEY IS'

println JWT_KEY_CRED_ID

println HUB_ORG

println SFDC_HOST

println CONNECTED_APP_CONSUMER_KEY



stage('checkout source') {

// when running in multi-branch job, one must issue this command

checkout scm

println 'Successfull checkout'

}

withEnv(["HOME=${env.WORKSPACE}"]) {

withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {

println 'Successfull login'

stage('Deploye Code') {

println 'Deploy start'

if (isUnix()) {

//rc = sh returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"

rc = sh "sfdx force:auth:jwt:grant --clientid $CONNECTED_APP_CONSUMER_KEY --username $HUB_ORG --jwtkeyfile $jwt_key_file --instanceurl $SFDC_HOST"

println 'login success'

}else{

println 'Successfull checkout 22'

rr = bat returnStatus: true, script: "sfdx force:auth:logout --username ${HUB_ORG}"

rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile "${jwt_key_file}" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"

}

if (rc != 0) { error 'hub org authorization failed' }

println rc

println 'Deploy start2'

// need to pull out assigned username

if (isUnix()) {

//rmsg = sh returnStdout: true, script: "sfdx force:mdapi:deploy -d DevSandbox/. -u ${HUB_ORG}"

rmsg = sh "sfdx force:mdapi:deploy -d output --targetusername $HUB_ORG --wait 20"

println 'Deploy succes'

}else{

rr = bat returnStatus: true, script: "sfdx force:auth:logout --username ${HUB_ORG}"

rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile "${jwt_key_file}" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"

rmsg = bat returnStdout: true, script: "sfdx force:source:deploy -x manifest/package.xml -u ${HUB_ORG}  --wait 20"

}

if (rc != 0) { error 'hub org authorization failed' }

println rc

println 'Deploy start2'

printf rmsg

println('Hello from a Job DSL script!')

println(rmsg)

}

}

}

}
