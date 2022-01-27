pipeline {
    agent any
    stages {
        stage("Build") {
		steps{
		  echo 'Shell command'
		  shell(readFileFromWorkspace("/tmp/script.sh"))
		}
	}
    }
}
