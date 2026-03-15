pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Hello from GitHub webhook!'
                echo "Branch: ${env.BRANCH_NAME}"
                echo "Commit: ${env.GIT_COMMIT}"
                sh 'git log -1 --pretty=format:"Last commit: %s"'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finished!'
        }
    }
}//asd