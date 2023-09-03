pipeline {
    agent any
    stages {
        
        stage ("Checkout") {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '94a40f2f-b98d-4903-90f9-53f128248e8b', url: 'https://github.com/chiemelaumeh/javaApp-JenkinsPipeline']])
                
                
            }
        }
        
        stage ("Build") {
            steps {
            sh "mvn clean install -f myWebApp/pom.xml"
                
            }
        }
        
         stage ("Code Quality") {
            steps {
            withSonarQubeEnv('SonarQube') {
                
              sh"mvn -f myWebApp/pom.xml sonar:sonar"
            }
                
            }
        }

      //    stage("Quality Gate"){
      //        steps {

                 
      //     timeout(time: 1, unit: 'HOURS') {
      //         def qg = waitForQualityGate()
      //         if (qg.status != 'OK') {
      //             error "Pipeline aborted due to quality gate failure: ${qg.status}"
      //         }
      //     }
      //   }
      // }        
       
        
        stage("Code Coverage") {
            steps {
                jacoco()
            }
        }
        
        
        
        stage ("Nexus Upload") {
            steps {
                
                nexusArtifactUploader artifacts: [[artifactId: 'myWebApp', classifier: '', file: 'myWebApp/target/myWebApp.war', type: 'war']], credentialsId: '81173d8c-1791-4db4-9289-194d0241e87e', groupId: 'com.dept.app', nexusUrl: 'ec2-18-217-252-27.us-east-2.compute.amazonaws.com:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
                
            }
        }
        
        stage("DEV Deploy"){
            steps {
                echo "deploying to DEV Env"
                deploy adapters: [tomcat9(credentialsId: 'aa2723f2-7bcf-46ad-9ebc-cec503e19b5d', path: '', url: 'http://ec2-3-22-130-125.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
                
            }
        }
        
        stage("DEV Notify"){
            steps {
                slackSend channel: 'mydeclarativepipelinejob', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
            }
        }
        
        stage("DEV Approve") {
            steps{
                echo "Taking approval from DEV Manager for QA Deployment"
                timeout(time: 7, unit: "DAYS") {
                    input message: "Do you approve QA Deployment?", submitter: "admin"
                }
            }
        }
        
        
        stage("QA Deploy"){
            steps {
                echo "deploying to QA Env"
                deploy adapters: [tomcat9(credentialsId: 'aa2723f2-7bcf-46ad-9ebc-cec503e19b5d', path: '', url: 'http://ec2-3-22-130-125.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
                
            }
        }
        
        stage("QA Notify"){
            steps {
                slackSend channel: 'mydeclarativepipelinejob', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
            }
        }
        
        stage("QA Approve") {
            steps{
                echo "Taking approval from QA Manager for PROD Deployment"
                timeout(time: 7, unit: "DAYS") {
                    input message: "Do you approve PROD Deployment?", submitter: "admin"
                }
            }
        }
        
        stage("PROD Deploy"){
            steps {
                echo "deploying to PROD Env"
                deploy adapters: [tomcat9(credentialsId: 'aa2723f2-7bcf-46ad-9ebc-cec503e19b5d', path: '', url: 'http://ec2-3-22-130-125.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
                
            }
        }
        
        stage("FINAL Notify"){
            steps {
                slackSend channel: 'mydeclarativepipelinejob', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
            }
        }
        
    }
}
