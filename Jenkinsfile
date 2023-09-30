pipeline {
  agent any

  tools {
    maven 'M2_HOME'
    
    }
  environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY') 
  } 
  
  stages {
   stage('CheckOut') {
      steps {
        echo 'Checkout the source code from GitHub'
        git branch: 'main', url: 'https://github.com/Gopala230390/Healthcare.git'
            }
    }
    
    stage('Package the Application') {
      steps {
        echo " Packaging the Application"
        sh 'mvn clean package'
            }
    }
    
    stage('Publish Reports using HTML') {
      steps {
      publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Healthcare/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
    }
 
   stage('Create Docker image of App') {
       steps {
         sh 'docker build -t gopala230390/health:4.0 .'
             }
         }

    stage('Docker Image Push') {
       steps {
       withCredentials([usernamePassword(credentialsId: 'dockerpass', passwordVariable: 'dockerpass', usernameVariable: 'dockerhub')]) {
         sh 'docker login -u ${dockerhub} -p ${dockerpass}'
       }
         sh 'docker push gopala230390/health:4.0'
   }    
     }   
  
    stage('Push Image to DockerHub') {
      steps {
        sh 'docker push gopala230390/health:4.0'
            }
    } 

        stage ('Configure Test-server with Terraform, Ansible and then Deploying'){
            steps {
                dir('my-serverfiles'){
                sh 'sudo chmod 600 AWS-EC2-Key.pem'
                sh 'terraform init'
                sh 'terraform validate'
                sh 'terraform apply --auto-approve'
                }
            }
        }
     stage('deploy the application to kubernetes'){
steps{
  sh 'sudo chmod 600 AWS-EC2-Key.pem'    
  sh 'sudo scp -o StrictHostKeyChecking=no -i AWS-EC2-Key.pem deploymentservice.yml ubuntu@52.91.145.150:/home/ubuntu/'
  

}
}
       /* stage ('Deploy into test-server using Ansible') {
           steps {
             ansiblePlaybook credentialsId: 'jenkinskey', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory', playbook: 'healthcare-playbook.yml'
           }
               }
  
        stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
    }
  }
}*/
      }
        }

  




          
