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
   

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: 'SIT-SECRET-FILE', variable: 'jwt_key_file')]) {
                    sh 'sfdx force:auth:logout --targetusername --username -p & sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}'
                    sh "sfdx essentials:filter-metadatas -i ./src -o ./package -p ./src/package.xml"
                    sh(returnStdout: false, script: '''
                        if [ -d ./package/classes ] 
                        then 
                            sfdx force:mdapi:deploy -d ./package -u sit -l RunSpecifiedTests -w -1 -g -r \$(sh testclasses.sh ./package/classes)
                        else 
                            sfdx force:mdapi:deploy -d ./package -u sit -l NoTestRun -w -1 -g 
                        fi
                    '''.stripIndent())
                }
}
