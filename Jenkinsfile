pipeline {
    agent any
    
    tools {
        maven 'MyMaven'  
    }
    

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    git branch: 'main', credentialsId: '', url: 'https://github.com/padmavathimudunuri/MavenProject.git'
                }
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Install') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Dependency-Check') {
            steps {
                dependencyCheck additionalArguments: '--format XML', odcInstallation: 'Dependency-Check' // The name given in Global Tool Configuration
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool name:'LocalSonar'
                    echo "Using SonarQube Scanner at: ${scannerHome}"
                    
                    withCredentials([usernamePassword(credentialsId: 'SonarCredentials', usernameVariable: 'SonarUser', passwordVariable: 'SonarPassword')]) {

                    withSonarQubeEnv('Sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=first-project -Dsonar.sources=. -Dsonar.java.binaries=$JavaBinariesPath -Dsonar.host.url=http://localhost:9000 -Dsonar.login=$SonarUser -Dsonar.password=$SonarPassword"
                    }
                    
                  }
              }
           }
        }
        
        stage('Reports') {
            steps {
                script {
                    def dependencyReportFile = 'dependency-check-report.xml'
                    //def sonarReportFile = '' 

                    archiveArtifacts artifacts: dependencyReportFile, allowEmptyArchive: false
                }
            }
        }
        // Other stages...
    }
}
