pipeline {
  agent any

  environment {
    IMAGE = "local-test-image:${BUILD_NUMBER}"
    DOCKERFILE = 'Dockerfile'
    CONTEXT = '.'
  }

  stages {

    stage('Prepare') {
      steps {
        // Xóa sạch workspace để tránh file cũ
        deleteDir()
        // Checkout source code
        checkout scm
      }
    }

    stage('Build Maven') {
      steps {
        // Ưu tiên mvnw, fallback sang mvn nếu không có
        sh '''
          if [ -f "./mvnw" ]; then
            ./mvnw -B clean package -DskipTests
          else
            mvn -B clean package -DskipTests
          fi
        '''
      }
    }

    stage('Build Docker Image Locally') {
      steps {
        sh '''
          echo "Building local Docker image: ${IMAGE}"
          docker build --no-cache -f "${DOCKERFILE}" -t "${IMAGE}" ${CONTEXT}
          docker images | grep "${IMAGE}" || true
        '''
      }
    }
  }

  post {
    always {
      // Dọn image sau khi build (tránh đầy disk Jenkins)
      sh 'docker rmi ${IMAGE} || true'
    }
  }
}
