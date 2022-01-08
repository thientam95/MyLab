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
                script {

                def NexusRepo = Version.endsWith("SNAPSHOT") ? "MyLab-SNAPSHOT" : "MyLab-RELEASE"

                nexusArtifactUploader artifacts: 
                [[artifactId: "${ArtifactId}",
                 classifier: '', 
                 file: "target/${ArtifactId}-${Version}.war", 
                 type: 'war']], 
                 credentialsId: 'nexus', 
                 groupId: "${GroupId}", 
                 nexusUrl: '13.212.122.168:8081', 
                 nexusVersion: 'nexus3', 
                 protocol: 'http', 
                 repository: "${NexusRepo}", 
                 version: "${Version}"
                }
            }
        }

        stage ('Deploy'){
            steps {
                echo "Deploying...."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'AnsibleController', 
                    transfers: [
                        sshTransfer(
                            cleanRemote: false, 
                            execCommand: 'ansible-playbook /home/ansibleadmin/playbooks/download-deploy-docker.yaml -i /home/ansibleadmin/playbooks/hosts', 
                            execTimeout: 120000, 
                        )], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)])
            }
        }
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

    