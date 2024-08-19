pipeline {
    // Specify the agent to run the pipeline
    agent any
    // Set environment variables for the pipeline
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    // Define the stages of the pipeline
    stages {
	
	stage('Checkout') {
            steps {
                // Checkout the code from your source control repository
				// Log message to indicate build start
                echo "----------- Checkout the code from your source control repository ----------"
                git branch: 'main', url: 'https://github.com/RafeeqAWSDevOps/saidemytrend.git'
				 echo "----------- Checkout the code from your source control repository ----------"
            }
        }
        // Stage for building the project
        stage("build") {
            steps {
                // Log message to indicate build start
                echo "----------- build started ----------"
                // Run Maven clean and deploy commands, skipping tests
                sh 'mvn clean deploy'
                // Log message to indicate build completion
                echo "----------- build completed ----------"
            }
        }
       
        // Stage for SonarQube analysis
        stage('SonarQube analysis') {
            environment {
                // Set the SonarQube scanner tool
                scannerHome = tool 'rafeeq-sonar-scanner'
            }
            steps {
                // Execute SonarQube analysis within the SonarQube environment
                withSonarQubeEnv('rafeeq-sonar-qube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
	}
}
