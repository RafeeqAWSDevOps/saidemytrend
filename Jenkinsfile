def registry = 'https://rafeeqdevops.jfrog.io/'
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
	    stage("Jar Publish") {
            steps {
                script {
                    // Log message to indicate JAR publish start
                    echo '<--------------- Jar Publish Started --------------->'
                    // Define the Artifactory server
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "jfrog-cred"
                    // Set properties for the build
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                    // Define the upload specification
                    def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "raf-libs-release-local/{1}",
                              "flat": "false",
                              "props": "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                    // Upload the files to Artifactory and collect build info
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    // Publish the build information to Artifactory
                    server.publishBuildInfo(buildInfo)
                    // Log message to indicate JAR publish end
                    echo '<--------------- Jar Publish Ended --------------->'
                }
            }
		}
    }
}
