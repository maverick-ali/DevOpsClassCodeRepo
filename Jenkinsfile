pipeline{
    tools{
        jdk 'Java'
        maven 'Maven'
    }
    
    agent any
    stages{
        /*
            stage('checkout'){
                agent any
                steps{
                    git 'https://github.com/maverick-ali/DevOpsClassCodeRepo.git'
                }
            }
            */
            stage('Compile'){
                agent any
                steps{
                    sh 'mvn compile'
                }
            }
            stage('CodeReview'){
                agent any
                steps{
                    sh 'mvn pmd:pmd'
                }
                post{
                    always{
                        recordIssues(tools: [pmdParser(pattern: 'target/pmd.xml')])
                    }
                }
            }
            stage('UnitTest'){
                agent any
                steps{
                    sh 'mvn test'
                }
                post{
                    always{
                        junit 'target/surefire-reports/*.xml'
                    }
                }
                
            }
            stage('MetricCheck'){
                agent any
                steps{
                    sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
                }
                post{
                    always{
                        cobertura coberturaReportFile: 'target/site/cobertura/coverage.xml'
                    }
                }
            }
            stage('Package'){
                //agent {label 'ubuntu_slave'}
                agent any
                steps{
                    sh 'mvn package'
                }
            }
            stage('Deploy'){
                agent { dockerfile true }
                steps{
                    sh 'cp /var/lib/jenkins/workspace/DemoPipelineASCode@2/target/addressbook.war .'
                    sh 'sudo docker build -t maverickali/devopstraining:addressbookImage$BUILD_NUMBER .'
                    sh 'sudo docker run -itd -P maverickali/devopstraining:addressbookImage$BUILD_NUMBER'
                }
            }
    }
}
