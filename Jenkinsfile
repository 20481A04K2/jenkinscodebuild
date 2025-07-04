pipeline {
  agent any
  environment {
    AWS_REGION = 'ap-south-1'
    CODEBUILD_PROJECT = 'vamsi-codebuild-project'
    GITHUB_REPO_URL = 'https://github.com/20481A04K2/awsfrontendecs.git'
    SERVICE_ROLE_ARN = 'arn:aws:iam::337243655832:role/service-role/codebuild-vamsi-project-service-role'
  }

  stages {
    stage('Check and Create CodeBuild Project') {
      steps {
        script {
          sh """
          echo "🔍 Checking if CodeBuild project exists..."
          PROJECT_EXISTS=\$(aws codebuild batch-get-projects --names $CODEBUILD_PROJECT --region $AWS_REGION --query 'projects[0].name' --output text 2>/dev/null)

          if [ "\$PROJECT_EXISTS" = "None" ] || [ -z "\$PROJECT_EXISTS" ]; then
            echo "🚀 CodeBuild project does not exist. Creating..."
            aws codebuild create-project \
              --name $CODEBUILD_PROJECT \
              --source type=GITHUB,location=$GITHUB_REPO_URL \
              --artifacts type=NO_ARTIFACTS \
              --environment type=LINUX_CONTAINER,image=aws/codebuild/standard:7.0,computeType=BUILD_GENERAL1_SMALL,privilegedMode=true \
              --service-role $SERVICE_ROLE_ARN \
              --region $AWS_REGION
          else
            echo "✅ CodeBuild project already exists: \$PROJECT_EXISTS"
          fi
          """
        }
      }
    }

    stage('Trigger CodeBuild') {
      steps {
        script {
          sh """
          echo "▶️ Starting CodeBuild project..."
          aws codebuild start-build \
            --project-name $CODEBUILD_PROJECT \
            --region $AWS_REGION
          """
        }
      }
    }
  }
}
