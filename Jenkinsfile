pipeline {
    agent any
    environment {
          AWS_ACCESS_KEY_ID     = credentials('creds')
          AWS_SECRET_ACCESS_KEY = credentials('creds')
    }
 
    stages {
        stage('Retrieve Instance IDs') {
            steps {
                script {
                    try {
                        // Execute AWS CLI command to describe instances launched by the autoscaling group
                        def awsCliCommand = "aws autoscaling describe-auto-scaling-instances --query 'AutoScalingInstances[?AutoScalingGroupName==`testasg`].[InstanceId]' --output text"
                        def instanceIds = sh(script: awsCliCommand, returnStdout: true).trim()
 
                        // Split the output into individual instance IDs
                        def instanceIdList = instanceIds.tokenize()
 
                        echo "Instance IDs launched by autoscaling group 'asg-test':"
                        instanceIdList.each { instanceId ->
                            echo instanceId
                        }
                    } catch (Exception e) {
                        echo "Error retrieving instance IDs: ${e.message}"
                    }
                }
            }
        }
    }
}
