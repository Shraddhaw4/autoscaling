pipeline {
    agent any
    // environment {
    //       AWS_ACCESS_KEY_ID     = credentials('creds')
    //       AWS_SECRET_ACCESS_KEY = credentials('creds')
    // }
    parameters { 
        extendedChoice name: 'Function', description: 'Select the function type:', type: 'PT_RADIO', descriptionPropertyValue: 'App,Web', value: 'app,web' 
    }
 
    stages {
        stage('User') {
            steps {
                wrap([$class: 'BuildUser']) {
                  script {
                     USER_ID = "${BUILD_USER}"
                  }
                }
                echo "User is : ${USER_ID}"
            }
        }
        stage('Retrieve Autoscaling Groups') {
            steps {
                script {
                    try {
                        def selectedOption = params.Function
                        def cred
                        def fun

                        switch (selectedOption) {
                            case 'app':
                                cred = 'creds'
                                fun = 'app'
                                break
                            case 'web':
                                cred = 'AWS_credentials'
                                fun = 'web'
                                break
                            default:
                                echo "Invalid option selected"
                        }

                        echo "Credentials: ${cred}"
                        echo "Function: ${fun}"

                        withCredentials([[
                            $class: 'AmazonWebServicesCredentialsBinding', 
                            accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                            secretKeyVariable: 'AWS_SECRET_ACCESS_KEY', 
                            credentialsId: "${cred}"]]) 
                            {
                        def awsCliCommand = "aws autoscaling describe-auto-scaling-groups --query 'AutoScalingGroups[?Tags[?Key==`type` && Value==${fun}]].AutoScalingGroupName' --output text"
                        def asgNames = sh(script: awsCliCommand, returnStdout: true).trim()
 
                        // Split the output into individual autoscaling group names
                        def asgNameList = asgNames.tokenize()
 
                        echo "Autoscaling groups with tag 'type=app':"
                        asgNameList.each { asgName ->
                            echo asgName
                            }
                        }
                        // Execute AWS CLI command to describe autoscaling groups with tag 'type=app'

                        // def awsCliCommand = "aws autoscaling describe-auto-scaling-groups --query 'AutoScalingGroups[?Tags[?Key==`type` && Value=='${fn}']].AutoScalingGroupName' --output text"
                        // def asgNames = sh(script: awsCliCommand, returnStdout: true).trim()
 
                        // // Split the output into individual autoscaling group names
                        // def asgNameList = asgNames.tokenize()
 
                        // echo "Autoscaling groups with tag 'type=app':"
                        // asgNameList.each { asgName ->
                        //     echo asgName
 
                        //     // Execute AWS CLI command to describe instances launched by each autoscaling group
                        //     def describeInstancesCmd = "aws autoscaling describe-auto-scaling-instances --query 'AutoScalingInstances[?AutoScalingGroupName==`${asgName}`].[InstanceId]' --output text"
                        //     def instanceIds = sh(script: describeInstancesCmd, returnStdout: true).trim()
 
                        //     // Split the output into individual instance IDs
                        //     def instanceIdList = instanceIds.tokenize()
 
                        //     // Tag instances with the 'user' tag and value 'app'
                        //     instanceIdList.each { instanceId ->
                        //         echo "Tagging instance ${instanceId}"
                        //         def tagCmd = "aws ec2 create-tags --resources ${instanceId} --tags Key=user,Value=${USER_ID}"
                        //         sh(script: tagCmd)
                        //     }
                        // }
                    } catch (Exception e) {
                        echo "Error retrieving or tagging autoscaling groups and instances: ${e.message}"
                    }
                }
            }
        }
    }
}

