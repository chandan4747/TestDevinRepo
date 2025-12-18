pipeline {
    agent any

    environment {
        PYTHON_VERSION = '3.9'
        APP_NAME = 'kushi-embroidery'
        VENV_DIR = 'venv'
        FLASK_APP = 'app.py'
        FLASK_ENV = 'production'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Setup Python Environment') {
            steps {
                echo 'Setting up Python virtual environment...'
                sh '''
                    python3 -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Code Quality - Lint') {
            steps {
                echo 'Running code quality checks...'
                sh '''
                    . ${VENV_DIR}/bin/activate
                    pip install flake8 pylint
                    
                    echo "Running Flake8..."
                    flake8 app.py --max-line-length=120 --ignore=E501,W503 || true
                    
                    echo "Running Pylint..."
                    pylint app.py --disable=C0114,C0115,C0116 --exit-zero
                '''
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running security vulnerability scan...'
                sh '''
                    . ${VENV_DIR}/bin/activate
                    pip install safety bandit
                    
                    echo "Checking dependencies for known vulnerabilities..."
                    safety check -r requirements.txt || true
                    
                    echo "Running Bandit security linter..."
                    bandit -r app.py -ll || true
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'Running unit tests...'
                sh '''
                    . ${VENV_DIR}/bin/activate
                    pip install pytest pytest-cov pytest-html
                    
                    # Create a basic test file if it doesn't exist
                    if [ ! -f tests/test_app.py ]; then
                        mkdir -p tests
                        cat > tests/test_app.py << 'EOF'
import pytest
import sys
import os
sys.path.insert(0, os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from app import app

@pytest.fixture
def client():
    app.config['TESTING'] = True
    with app.test_client() as client:
        yield client

def test_home_page(client):
    """Test that home page loads successfully"""
    response = client.get('/')
    assert response.status_code == 200
    assert b'Kushi Enterprises' in response.data

def test_static_css(client):
    """Test that CSS file is accessible"""
    response = client.get('/static/style.css')
    assert response.status_code == 200
EOF
                    fi
                    
                    pytest tests/ -v --html=reports/test-report.html --self-contained-html --cov=. --cov-report=html:reports/coverage || true
                '''
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: true,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'reports',
                        reportFiles: 'test-report.html',
                        reportName: 'Test Report'
                    ])
                }
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building application artifacts...'
                sh '''
                    . ${VENV_DIR}/bin/activate
                    
                    # Create distribution package
                    mkdir -p dist
                    
                    # Copy application files
                    cp -r app.py requirements.txt templates static dist/
                    
                    # Create version file
                    echo "BUILD_NUMBER=${BUILD_NUMBER}" > dist/version.txt
                    echo "BUILD_DATE=$(date -u +%Y-%m-%dT%H:%M:%SZ)" >> dist/version.txt
                    echo "GIT_COMMIT=${GIT_COMMIT}" >> dist/version.txt
                    
                    # Create tarball
                    tar -czvf ${APP_NAME}-${BUILD_NUMBER}.tar.gz -C dist .
                '''
            }
        }

        stage('Docker Build') {
            when {
                expression { 
                    fileExists('Dockerfile') || params.BUILD_DOCKER == true 
                }
            }
            steps {
                echo 'Building Docker image...'
                sh '''
                    # Create Dockerfile if it doesn't exist
                    if [ ! -f Dockerfile ]; then
                        cat > Dockerfile << 'EOF'
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
RUN pip install gunicorn

COPY . .

EXPOSE 5000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5000", "app:app"]
EOF
                    fi
                    
                    docker build -t ${APP_NAME}:${BUILD_NUMBER} .
                    docker tag ${APP_NAME}:${BUILD_NUMBER} ${APP_NAME}:latest
                '''
            }
        }

        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                echo 'Deploying to staging environment...'
                sh '''
                    echo "Deploying ${APP_NAME} version ${BUILD_NUMBER} to staging..."
                    # Add your staging deployment commands here
                    # Example: kubectl apply -f k8s/staging/
                    # Example: ssh staging-server "cd /app && ./deploy.sh"
                '''
            }
        }

        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying to production environment...'
                input message: 'Deploy to production?', ok: 'Deploy'
                sh '''
                    echo "Deploying ${APP_NAME} version ${BUILD_NUMBER} to production..."
                    # Add your production deployment commands here
                    # Example: kubectl apply -f k8s/production/
                    # Example: ssh prod-server "cd /app && ./deploy.sh"
                '''
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                               [pattern: '.propsfile', type: 'EXCLUDE']])
        }
        success {
            echo 'Pipeline completed successfully!'
            // Uncomment to enable Slack notifications
            // slackSend(color: 'good', message: "Build Successful: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
        failure {
            echo 'Pipeline failed!'
            // Uncomment to enable Slack notifications
            // slackSend(color: 'danger', message: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
        unstable {
            echo 'Pipeline is unstable!'
        }
    }
}
