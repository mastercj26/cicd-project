# cicd-project
tage 1: Git Checkout
Pulls source code from GitHub.
git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/jaiswaladi246/Boardgame.git'

Stage 2: Compile
Compiles Java code.
sh "mvn compile"

Stage 3: Test
Runs unit tests.
sh "mvn test"

Stage 4: File System Scan
Scans local project files using Trivy.
sh "trivy fs --format table -o trivy-fs-report.html ."

Stage 5: SonarQube Analysis
Runs static code analysis for bugs, vulnerabilities, code smells.
withSonarQubeEnv('sonar') {
    sh ''' $SCANNER_HOME/bin/sonar-scanner ... '''
}

Stage 6: Quality Gate
Checks SonarQube Quality Gate (pass/fail).

Stage 7: Build (Package)
Builds the final .jar file.
sh "mvn package"

Stage 8: Publish to Nexus
Uploads .jar to Nexus repository.
mvn deploy

Stage 9: Build & Tag Docker Image
Creates Docker image of the app.
docker build -t adijaiswal/boardshack:latest .

Stage 10: Docker Image Scan
Scans image with Trivy.
trivy image -o trivy-image-report.html adijaiswal/boardshack:latest

Stage 11: Push Docker Image
Pushes image to DockerHub.
docker push adijaiswal/boardshack:latest

Stage 12: Deploy to Kubernetes
Applies deployment YAML to K8s cluster.
kubectl apply -f deployment-service.yaml

Stage 13: Verify Deployment
Checks running Pods & Services.
kubectl get pods -n webapps
kubectl get svc -n webapps
