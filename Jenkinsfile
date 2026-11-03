pipeline{
  agent{
    label 'agent'
  }
  parameters {
    choice(
        name: 'BRANCH_NAME',
        choices: ['dev', 'main'],
        description: 'Select Git Branch'
    )
}
  stages{
    stage('checkout'){
      steps{
        git branch: "${params.BRANCH_NAME}",
            url: 'https://github.com/avi-devopshub/repo-frontend.git'           
      }
    }
    stage('push s3'){
      steps{
        sh 'aws s3 cp index.html s3://frontend-repo-537971897528-ap-south-1-an/${params.BRANCH_NAME}'
      }
    }
  }
}
