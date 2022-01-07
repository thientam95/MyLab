pipeline{
    //Directives
    agent any
    tools {
        maven 'maven'
    }
    environment {
        ArtifactId = readMavenPom().getArtifactId()
        Version = readMavenPom().getVersion()
        Name = readMavenPom().getName()
        GroupId = readMavenPom().getGroupId()
    }

    stages {
        // Specify various stage with in stages

        // stage 1. Build
        stage ('Build'){
            steps {
                sh 'mvn clean install package'
            }
        }

        // Stage2 : Testing
        stage ('Test'){
            steps {
                echo ' testing......'

            }
        }

        stage ('Print Env Var'){
            steps {
                echo "Artifact ID is '${ArtifactId}'"
                echo "Version is '${Version}'"
                echo "Group ID is '${GroupId}'"
                echo "Name is '${Name}'"
            }
        }

        stage ('Publish to Nexus'){
            steps {
                nexusArtifactUploader artifacts: 
                [[artifactId: '${ArtifactId}',
                 classifier: '', 
                 file: 'target/${ArtifactId}-${Version}.war', 
                 type: 'war']], 
                 credentialsId: 'nexus', 
                 groupId: '${GroupId}', 
                 nexusUrl: '18.142.91.252:8081', 
                 nexusVersion: 'nexus3', 
                 protocol: 'http', 
                 repository: 'MyLab-SNAPSHOT', 
                 version: '${Version}'
            }
        }

        // Stage3 : Publish the source code to Sonarqube
        // stage ('Sonarqube Analysis'){
        //     steps {
        //         echo ' Source code published to Sonarqube for SCA......'
        //         withSonarQubeEnv('sonarqube'){ // You can override the credential to be used
        //              sh 'mvn sonar:sonar'
        //         }

        //     }
        // }

        
        
    }

}