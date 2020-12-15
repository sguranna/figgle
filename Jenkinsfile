pipeline{
    agent any
    stages{
        stage('checkout'){
            steps{
                git 'https://github.com/sguranna/figgle.git'
            }
        }
        stage('Build'){
            steps{
            sh 'dotnet restore Figgle.sln'
            sh 'dotnet build --configuration Release Figgle.sln'
            sh 'dotnet publish -c Release Figgle/Figgle.csproj -f netstandard1.3'
            sh 'mkdir -p figgle-package/usr/bin/'
            sh 'cp Figgle/bin/Release/netstandard1.3/publish/* figgle-package/usr/bin/'
            sh 'dpkg-deb --build figgle-package'
            }
        }
        stage('DockerBuild'){
            steps{
                sh 'docker build -t figgle_image:latest .'
                withCredentials([usernamePassword(credentialsId: 'Dockerhub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'}
                sh 'docker tag figgle_image:latest sguranna1/figgle_image:latest'
                sh 'docker push sguranna1/figgle_image:latest' 
            }
		}
		stage('Multicontainer'){
            steps{
                sh 'docker-compose up -d'
            }
        }
    }
}
