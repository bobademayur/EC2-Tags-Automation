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
 
                    // Initialize an empty list to store tags
                    def tagList = []
 
                    // Dynamically create input steps for each tag key-value pair
                    for (int i = 1; i <= numberOfTags; i++) {
                        def tagInputs = input(
                            message: "Enter key and value for tag ${i}",
                            parameters: [
                                string(name: "TAG_KEY_${i}", description: "Enter key for tag ${i}"),
                                string(name: "TAG_VALUE_${i}", description: "Enter value for tag ${i}")
                            ]
                        )
                        // Add each key-value pair to the tag list
                        tagList.add("Key=${tagInputs["TAG_KEY_${i}"]},Value=${tagInputs["TAG_VALUE_${i}"]}")
                    }
 
                    // Store the tags in the environment variable as a space-separated string
                    env.TAGS_STRING = tagList.join(" ")
                }
            }
        }
        stage('Tag EC2 Instance') {
            steps {
                script {
                    // Construct and run the AWS CLI command to tag the EC2 instance
                    def command = "aws ec2 create-tags --resources ${env.INSTANCE_ID} --tags ${env.TAGS_STRING}"
                    echo "Running command: ${command}"
                    
                    sh command
                }
            }
        }
    }
}
