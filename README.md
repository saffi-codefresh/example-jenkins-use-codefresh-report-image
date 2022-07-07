# example-jenkins-use-codefresh-report-image
Example calling jenkins CI trigger codefresh report image

Using Pipeline configured with 
* definition: Pipeline script from scm
* SCM: GIT
* Repositories: https://github.com/codefresh-io/example-jenkins-use-codefresh-report-image.git
* Script path: Jenkinsfile

When the pipeline is run, it pulls the script from repo and run it. 
The script build and image from the Dockerfile and reports that to codefresh using codefresh-report-image container image.
