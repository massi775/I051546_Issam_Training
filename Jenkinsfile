properties(
    [[
        $class: 'BuildDiscarderProperty', 
        strategy: [$class: 'LogRotator', 
        artifactDaysToKeepStr: '', 
        artifactNumToKeepStr: '3', 
        daysToKeepStr: '', 
        numToKeepStr: '3'
    ]],

    disableConcurrentBuilds(),
    
    parameters([
        string(
            defaultValue: '', 
            description: "Enter your Firstname LastName", 
            name: 'NAME'
        ),
        choice(
            choices: ['FRANCE', 'GERMANY', 'SPAIN', 'SENEGAL', 'ITALY'],
            description: """
            Select your country
            Example : France, Germany, Spain, Senegal, Italy
            """, 
            name: 'COUNTRY'
        ),
        booleanParam(
            defaultValue: false, 
            description: "Are you a human ?", 
            name: 'CAPTCHA'
        )
    ])
])

node("master") {
    
    checkout scm
    
    def cmd
    def cmdResult
    def errMessage


try {

    stage("Know Your Customer"){
        
        if (currentBuild.result != 'FAILURE') {
        
            sh "
                #!/bin/bash

                echo "********* START: KNOW YOUR CUSTOMER *********"

                [ ! -z "$NAME" ] && echo "My name is $NAME and I live in $COUNTRY" || (echo "Please enter your name"; exit 1)

                [ $CAPTCHA == true ] && echo "Youpii I'm a human" || echo "What a hell was that, it's a robot"

                echo "********* END : KNOW YOUR CUSTOMER *********"
            "
        } else {

            println "The stage 'Know Your Customer' was skipped"
        }
    }

    stage("Getting Jenkins Env Vars"){
        
        if (currentBuild.result != 'FAILURE') {
        
            sh '
                #!/bin/bash

                echo "****** START : JENKINS ENVIRONMENT VARIABLES ******"

                echo "JENKINS_HOME =" ${env.JENKINS_HOME}
                echo "JENKINS BUILD_URL = $BUILD_URL"
                echo "JENKINS_BUILD_NUMBER = $BUILD_NUMBER"
                echo "JENKINS BUILD_TAG = $BUILD_TAG"
                echo "JENKINS NODE_NAME = $NODE_NAME"

                echo "****** END : JENKINS ENVIRONMENT VARIABLES ******"
            '
        } else {

            println "The stage 'Getting Jenkins Env Vars' was skipped"
        }
    }

} catch(Exception err){

        currentBuild.result = 'FAILURE'
        errMessage = err.getMessage()
        
        throw err
    
    } finally {

        if ( currentBuild.result == 'FAILURE' ) {

            emailext attachLog: false,
            body: """Dear colleague, <br><br> 
                    Unfortunately we found an issue while executing your first jenkins pipeline job <br>
                    <i style="color:red" >$errMessage</i><br>
                    Please check-out the following link for more information : ${env.BUILD_URL} <br><br>
                    Run Live, Run Simple <br> 
                    Quality Engineering / DevOps Infrastructure Team
                    """,
            //recipientProviders: [buildUser()],
            to:"issam.smat@sap.com",
            subject: "${env.JOB_NAME} => Status: ${currentBuild.currentResult}"

        } else {
                
            currentBuild.result = 'SUCCESS'

            emailext attachLog: false,
            body: """Dear colleague, <br><br> 
                    I'm excited to inform you that your first jenkins pipeline job was successfully executed <br><br>
                    Please check-out the following link for more information : ${env.BUILD_URL} <br><br>
                    Run Live, Run Simple <br> 
                    Quality Engineering / DevOps Infrastructure Team
                    """,
            //recipientProviders: [buildUser()],
            to:"issam.smat@sap.com",
            subject: "${env.JOB_NAME} => Status: ${currentBuild.currentResult}"
            
        }
    }
}