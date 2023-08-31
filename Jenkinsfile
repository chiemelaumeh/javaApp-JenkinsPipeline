pipeline {
    agent any
    stages {
        steps {
            
    stage ("checkout") {
    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '94a40f2f-b98d-4903-90f9-53f128248e8b', url: 'https://github.com/chiemelaumeh/javaApp-JenkinsPipeline']])
        
    }
    
        }
    stage ('build')  {
        steps {
            
    sh "mvn/bin/mvn clean install -f myWebApp/pom.xml"
        }

    }

    stage ('Code Quality scan')  {
        steps{
            
       withSonarQubeEnv('SonarQube') {
       sh "mvn/bin/mvn -f myWebApp/pom.xml sonar:sonar"
        }
        }
   }
   
    stage ('Code coverage')  {
        steps {
            
       jacoco()
        }
   }

    stage ('Nexus upload')  {
        
        steps {
            nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: 'ec2-3-21-33-28.us-east-2.compute.amazonaws.com:8081',
        groupId: 'myGroupId',
        version: '1.0-SNAPSHOT',
        repository: 'maven-snapshots',
        credentialsId: '81173d8c-1791-4db4-9289-194d0241e87e',
        artifacts: [
            [artifactId: 'myWebApp',
             classifier: '',
             file: 'myWebApp/target/myWebApp.war',
             type: 'war']
        ]
     )
        }
        
    }
    
    stage ('DEV Deploy') {
        steps {
            
     echo "deploying to DEV Env "
     deploy adapters: [tomcat9(credentialsId: 'aa2723f2-7bcf-46ad-9ebc-cec503e19b5d', path: '', url: 'http://ec2-3-138-126-199.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
        }
   }
   
    stage ('DEV Notify')  {
        steps {
            
       slackSend(channel:'automatedjob1-build', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
   }
    stage ('DEV Approve')  {
        steps {
            
            echo "Taking approval from DEV Manager for QA Deployment"     
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you approve QA Deployment?', submitter: 'admin'
            }
        }
     }
     
   
   
   stage ('QA Deploy')  {
       steps {
           
     echo "deploying into QA Env " 
     deploy adapters: [tomcat9(credentialsId: 'aa2723f2-7bcf-46ad-9ebc-cec503e19b5d', path: '', url: 'http://ec2-3-138-126-199.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
       }

}
 stage ('QA Notify')  {
     steps {
         
       slackSend(channel:'automatedjob1-build', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
     }
   }
    stage ('QA Approve')  {
        steps {
            
            echo "Taking approval from DEV Manager for QA Deployment"     
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you approve QA Deployment?', submitter: 'admin'
            }
        }
     }
    stage ('PROD Deploy')  {
        steps {
            
     echo "deploying into QA Env " 
     deploy adapters: [tomcat9(credentialsId: 'aa2723f2-7bcf-46ad-9ebc-cec503e19b5d', path: '', url: 'http://ec2-3-138-126-199.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
        }

}
 stage ('FINAL Notify')  {
     steps {
         
       slackSend(channel:'automatedjob1-build', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
     }
   }

     
        
    }


    
     
}
