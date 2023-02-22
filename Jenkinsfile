node{
  stage('Git Checkout'){
    git 'https://github.com/jamesfrostyy/spring-boot-hello-world/'
  }
  stage('Compile-Package'){
    './mvnw clean package'
  }
}
