pipeline{
    agent any

    stages{

        stage("build"){

            steps{
                echo "Building the App..."
                sh 'ls -la'
                sh 'cat app.py'
            }

        }

        stage("test"){

            steps{
                echo "Testing the App..."
            }

        }

        stage("deploy"){

            steps{
                echo "Deploying the App..."
            }

        }

    }    
}
