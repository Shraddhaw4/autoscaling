def describeAutoscalingGroupByTag(String tagValue) {
    try {
        def awsRegion = 'ap-south-1'
        def awsCredentialsId = 'creds'
 
        withAWS(region: awsRegion, credentials: awsCredentialsId) {
            // Describe the autoscaling groups with a specific tag value
            def autoscalingGroups = aws.autoscaling.describeAutoScalingGroups([Filters: [[Name: 'tag:type', Values: [tagValue]]]])
            echo "Autoscaling groups with tag 'type' and value '${tagValue}' details: ${autoscalingGroups}"
 
            // Iterate over each autoscaling group and assign tag to instances
            autoscalingGroups.AutoScalingGroups.each { autoscalingGroup ->
                // Get the instances launched by the autoscaling group
                def instances = autoscalingGroup.Instances
 
                // Assign tag to each instance with the name of the user who started the build
                def user = currentBuild.rawBuild.getCauses()[0].getUserId()
                instances.each { instance ->
                    aws.ec2.createTags(Resources: [instance.InstanceId], Tags: [Key: 'user', Value: user])
                    echo "Tag 'user' with value '${user}' assigned to instance '${instance.InstanceId}'"
                }
            }
        }
    } catch (Exception e) {
        echo "Error describing autoscaling groups with tag 'type' and value '${tagValue}': ${e.message}"
    }
}

pipeline {
    agent any
 
    parameters {
        extendedChoice(name: 'CHOICE_PARAM',
                        description: 'Select an option',
                        defaultValue: 'Option1',
                        multiSelectDelimiter: ',',
                        type: 'PT_RADIO',
                        value: 'Option1,Option2,Option3,Option4')
    }
 
    stages {
        stage('Describe Autoscaling Group') {
            steps {
                script {
                    // Access the value selected in the Extended Choice parameter
                    def selectedOption = params.CHOICE_PARAM
                    echo "Selected option: ${selectedOption}"
 
                    // Describe the autoscaling group based on the selected option
                    switch (selectedOption) {
                        case 'Option1':
                            describeAutoscalingGroupByTag('app')
                            break
                        case 'Option2':
                            describeAutoscalingGroupByTag('web')
                            break
                        case 'Option3':
                            describeAutoscalingGroupByTag('db')
                            break
                        case 'Option4':
                            describeAutoscalingGroupByTag('analytics')
                            break
                        default:
                            echo "No autoscaling group described for option: ${selectedOption}"
                    }
                }
            }
        }
    }
}
