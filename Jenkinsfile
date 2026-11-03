pipeline{
  agent{
    label 'agent'
  }
  options {
        skipDefaultCheckout()
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
        sh "aws s3 cp index.html s3://frontend-repo-537971897528-ap-south-1-an/${params.BRANCH_NAME}/"
      }
    }
    stage('checkout-diff-repo'){
    steps {
        withCredentials([usernamePassword(credentialsId: 'github-token', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PAT')]) {
            sh '''
                # 1. Clone using the token for authentication
                git clone https://${GIT_USER}:${GIT_PAT}@github.com/avi-devopshub/environment-branching.git
                cd environment-branching
                
                # 2. Configure Git identity locally (fix typos)
                git config user.name "Avinash"
                git config user.email "avinashmk1998@gmail.com"
                
                # 3. Create a unique feature branch
                BRANCH_NAME="update-image-${BUILD_NUMBER}"
                git checkout -b ${BRANCH_NAME}
                
                # 4. Perform your file updates (e.g., adding image.txt or running yq)
                touch image.txt
                git add image.txt
                git commit -m "chore: automated image tag update"
                
                # 5. Push the feature branch to GitHub
                git push origin ${BRANCH_NAME}
                
                # 6. Open the Pull Request using GitHub CLI (gh)
                # Assumes TARGET_BRANCH is passed as a parameter (e.g., dev, qa, main)
                gh pr create --base ${TARGET_BRANCH} --head ${BRANCH_NAME} --title "Update Image Tag for ${TARGET_BRANCH}" --body "Automated update from Jenkins build #${BUILD_NUMBER}"
            '''
        }
    }
}
  }
}
