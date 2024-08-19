pipeline {
    agent any
    stages {
        stage('Get Instance ID') {
            steps {
                script {
                    // Prompt the user for the instance ID
                    env.INSTANCE_ID = input message: 'Enter the Instance ID to tag:', parameters: [string(name: 'INSTANCE_ID', defaultValue: '')]
                }
            }
        }
        stage('Get Number of Tags') {
            steps {
                script {
                    // Prompt the user for the number of tags
                    env.NUMBER_OF_TAGS = input message: 'How many tags do you want to add?', parameters: [string(name: 'NUMBER_OF_TAGS', defaultValue: '1')]
                    env.NUMBER_OF_TAGS = env.NUMBER_OF_TAGS.toInteger()
                }
            }
        }
        stage('Get Tags') {
            steps {
                script {
                    def tags = [:]
                    for (int i = 1; i <= env.NUMBER_OF_TAGS; i++) {
                        // Prompt the user for each tag key and value
                        def tagKey = input message: "Enter key for tag ${i}:", parameters: [string(name: "TAG_KEY_${i}", defaultValue: '')]
                        def tagValue = input message: "Enter value for tag ${i}:", parameters: [string(name: "TAG_VALUE_${i}", defaultValue: '')]
                        tags[tagKey] = tagValue
                    }
                    // Store tags as a JSON string in the environment variable
                    env.TAGS_JSON = new groovy.json.JsonBuilder(tags).toString()
                }
            }
        }
        stage('Tag EC2 Instance') {
            steps {
                script {
                    def tags = new groovy.json.JsonSlurper().parseText(env.TAGS_JSON)
                    def tagString = tags.collect { key, value -> "Key=${key},Value=${value}" }.join(" ")
                    
                    // Construct and run the AWS CLI command to tag the EC2 instance
                    def command = "aws ec2 create-tags --resources ${env.INSTANCE_ID} --tags ${tagString}"
                    echo "Running command: ${command}"
                    
                    sh command
                }
            }
        }
    }
}
