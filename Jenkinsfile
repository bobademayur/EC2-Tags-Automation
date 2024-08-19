pipeline {
    agent any
    stages {
        stage('Get Instance ID and Tags') {
            steps {
                script {
                    // Prompt the user for the instance ID and number of tags
                    def inputs = input(
                        message: 'Provide the instance ID and tags',
                        parameters: [
                            string(name: 'INSTANCE_ID', description: 'Enter the Instance ID to tag'),
                            string(name: 'NUMBER_OF_TAGS', description: 'How many tags do you want to add?', defaultValue: '1')
                        ]
                    )
 
                    env.INSTANCE_ID = inputs['INSTANCE_ID']
                    def numberOfTags = inputs['NUMBER_OF_TAGS'].toInteger()
 
                    // Initialize an empty map to store tags
                    def tags = [:]
 
                    // Dynamically create input steps for each tag key-value pair
                    for (int i = 1; i <= numberOfTags; i++) {
                        def tagInputs = input(
                            message: "Enter key and value for tag ${i}",
                            parameters: [
                                string(name: "TAG_KEY_${i}", description: "Enter key for tag ${i}"),
                                string(name: "TAG_VALUE_${i}", description: "Enter value for tag ${i}")
                            ]
                        )
                        // Add each key-value pair to the tags map
                        tags[tagInputs["TAG_KEY_${i}"]] = tagInputs["TAG_VALUE_${i}"]
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
has context menu
