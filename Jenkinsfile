pipeline {
  agent any

  environment {
    IMAGE = "local-test-image:${BUILD_NUMBER}"
    DOCKERFILE = 'Dockerfile'
    CONTEXT = '.'
  }

//   stages {
//     stage('Prepare') {
//       steps {
//         // xóa sạch workspace để tránh file cũ
//         deleteDir()
//         // checkout 1 lần duy nhất
//         checkout scm
//       }
//     }

    stage('Inspect workspace & git') {
      steps {
        sh '''
          echo "PWD: $(pwd)"
          git remote -v || true
          echo "Branch: $(git rev-parse --abbrev-ref HEAD 2>/dev/null || true)"
          echo "Commit: $(git rev-parse --short HEAD 2>/dev/null || true)"
          echo "Top-level files:"
          ls -la

          echo "Dockerfile content:"
          if [ -f "${DOCKERFILE}" ]; then sed -n '1,200p' "${DOCKERFILE}"; else echo "ERROR: ${DOCKERFILE} not found"; exit 1; fi

          echo "Search for any openjdk occurrences:"
          grep -R --line-number "FROM openjdk" . || true
        '''
      }
    }

    stage('Build Maven') {
      steps {
        // dùng mvnw nếu có, fallback sang mvn
        sh './mvnw -B clean package -DskipTests || mvn -B clean package -DskipTests'
      }
    }

    stage('Build Docker Image Locally') {
      steps {
        sh '''
          echo "Building local Docker image: ${IMAGE} using ${DOCKERFILE}"
          docker build --no-cache -f "${DOCKERFILE}" -t "${IMAGE}" ${CONTEXT}
          docker images --format '{{.Repository}}:{{.Tag}} {{.ID}} {{.Size}}' | grep "${IMAGE}" || true
        '''
      }
    }

    stage('Run Smoke Test (Optional)') {
      steps {
        sh '''
          docker run --rm -d --name test-${BUILD_NUMBER} -p 8080:8080 "${IMAGE}" || true
          sleep 4
          docker ps --filter "name=test-${BUILD_NUMBER}" --format "table {{.Names}}\\t{{.Image}}\\t{{.Status}}"
          docker stop test-${BUILD_NUMBER} || true
        '''
      }
    }
  }

  post {
    always {
      sh 'docker rmi ${IMAGE} || true'
    }
  }
}
