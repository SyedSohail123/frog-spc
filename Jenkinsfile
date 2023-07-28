pipeline {
    agent { label 'MAVEN_JDK8' }
    triggers { pollSCM('* * * * *') }
    tools {
        jdk 'JDK_17'
        maven 'MAVEN-3.9'
    }
    stages {
        stage('VCS') {
            steps {
                git url: 'https://github.com/SyedSohail123/frog-spc.git',
                    branch: 'master'
            }
        }       
        stage('version') {
            steps {
                sh 'java -version'
                sh 'mvn --version' 
            }
        }
        stage('Publish artifacts to Jfrog') {
            steps {
                rtMavenDeployer (
                    tool: 'default'
                    id: "MAVEN_DEPLOYER",
                    serverId: "Jfrog_Devops",
                    releaseRepo: 'qtdevops-libs-release-local',
                    snapshotRepo: 'qrdevops-libs-snapshot-local'
                )
                rtMavenRun (                    
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER"
                )
                rtPublishBuildInfo (
                    serverId: "Jfrog_Devops"
                )
            }
        }
        stage('Archiving the artifacts & publishing test results') {
            steps {
                archiveArtifacts onlyIfSuccessful: true,
                            artifacts: '**/target/*.jar'
                junit testResults: '**/surefire-reports/TEST-*.xml' 
            }
        }
    }
}