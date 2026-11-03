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
                rm -rf environment-branching
                
                git clone https://${GIT_USER}:${GIT_PAT}@github.com/avi-devopshub/environment-branching.git
                cd environment-branching
                
                # Fetch all remote branches and history so GitHub CLI can track them
                git fetch origin
                
                git config user.name "Avinash"
                git config user.email "avinashmk1998@gmail.com"
                
                BRANCH_NAME="update-image-${BUILD_NUMBER}"
                git checkout -b ${BRANCH_NAME}
                
                touch image.txt
                git add image.txt
                git commit -m "chore: automated image tag update"
                
                git push origin ${BRANCH_NAME}
                
                DEST_BRANCH="${TARGET_BRANCH:-main}"
                
                GH_TOKEN="${GIT_PAT}" gh pr create --base "${DEST_BRANCH}" --head "${BRANCH_NAME}" --title "Update Image Tag for ${DEST_BRANCH}" --body "Automated update from Jenkins build #${BUILD_NUMBER}"
            '''
        }
    }
}
  }
}
