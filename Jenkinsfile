pipeline {
    agent any
    environment {
        HOST = "98.81.239.161"
    }

    stages{
        stage("checkout"){
            steps{
                git(
                    url: "https://github.com/sanjay2028/jenkins-poc.git",
                    branch: "development"
                )

                echo "Checkout was successful"

            }
        }

        stage("BUILD"){
            steps{
                sh """
                    jq . server.json

                """
            }
        }
    }

}