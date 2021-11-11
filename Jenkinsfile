pipeline {
  agent  any
    stages {

      stage ('Checkout SCM'){
        steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/para93/Terraform-Jenkins-AWS.git']]])
        }
      }

     
      stage('Set Terraform path') {
       steps {
         script {
            def tfHome = tool name: 'terraform'
            env.PATH = "${tfHome}:${env.PATH}"
         }
     }
  }
  stage('terraform init') {
 
       steps {
           dir ("vpc") {
                script {
                    withAWS(roleAccount:'308107017754', role:'mchung-ec2', useNode: true) {
                    sh 'terraform init -no-color'
                    }
             }
           }
        }
      }

  stage('terraform Plan') {
 
       steps {
           dir ("vpc") {
            
               script {
                    withAWS(roleAccount:'308107017754', role:'mchung-ec2', useNode: true) {
                    sh 'terraform plan -no-color -out=plan.out'
                    }
               }
            }
        }
      }

  stage('Waiting for Approvals') {
            
      steps{
          input('Plan Validated? Please approve to create VPC Network in AWS?')
			  }
      }    

  stage('terraform Apply') {
 
       steps {
           dir ("vpc") {
            
              script {
                    withAWS(roleAccount:'308107017754', role:'mchung-ec2', useNode: true) {
                    sh 'terraform apply -no-color -auto-approve plan.out'
                    sh "terraform output"
                    }
              }
            
           }
        }
      }
   }
}
