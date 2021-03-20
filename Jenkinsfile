pipeline {
    environment { 
        registry = "kappajerem/docker-django-jenkins-test" 
        registryCredential = '01aa3af6-bdf9-4d61-8b1c-b3a7ef0f2823' 
        dockerImage = '' 
    }
 agent {docker {image 'python:3.7.2'}}

 stages {
  stage('Cloning our Git') { 
    steps { 
     git 'https://github.com/jerem-uzoma/django-markdown-editor.git' 
    }
  } 
  stage('Build Code') {
   steps {
    sh "mkdir .pip_cache"
    sh "cache restore"
    sh "pip install --cache-dir .pip_cache -r requirements.txt"
    sh "cache store"
   }
  }

  stage('Unit test') {
   steps {
    sh "cd martor_demo"
    sh "python manage.py makemigrations"
    sh "python manage.py migrate"
    sh "python manage.py test"
   }
  }

  stage('Checklist') {
   steps {
    sh "cd martor_demo"
    sh "python manage.py check --deploy"
   }
  } 

  stage('Check Lint') {
   steps {
    sh "pip install flake8"
    sh "flake8 martor_demo/ --max-line-length=127"
   }
  }

  stage('Building our image') { 
    steps { 
        script { 
            dockerImage = docker.build registry + ":$BUILD_NUMBER" 
           }
    } 
  }
  stage('Deploy our image') { 
    steps { 
        script { 
            docker.withRegistry( '', registryCredential ) { 
                dockerImage.push() 
            }
        } 
    }
  }
  stage('Cleaning up') { 
    steps { 
        sh "docker rmi $registry:$BUILD_NUMBER" 
    }
  }
 }
}