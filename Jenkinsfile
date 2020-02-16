#!groovy


//格式化输出
def PrintMes(value,color){
    colors = ['red'   : "\033[40;31m >>>>>>>>>>>${value}<<<<<<<<<<< \033[0m",
    'blue'  : "\033[47;34m ${value} \033[0m",
    'green' : "[1;32m>>>>>>>>>>${value}>>>>>>>>>>[m",
    'green1' : "\033[40;32m >>>>>>>>>>>${value}<<<<<<<<<<< \033[0m" ]
    ansiColor('xterm') {
        println(colors[color])
    }
}


//Pipeline
pipeline {
    agent { node {  label "master"   //指定运行节点的标签或者名称
               //    customWorkspace "${env.MyJenkinsWorkSpace}"   //指定运行工作目录（可选）
           }
       }
       environment{
        PATH = "/bin:/sbin:/usr/bin:/user/sbin:/usr/local/bin"
    }
    options { 
        skipDefaultCheckout()  //删除隐式checkout scm语句
        disableConcurrentBuilds() //禁止并行
        timeout(time: 1, unit: 'HOURS')  //流水线超时设置1h
    }

    stages {

    //下载代码
        stage("Initialization"){ //阶段名称 
            steps{  //步骤
                timeout(time:5, unit:"MINUTES"){   //步骤超时时间
                    script{ //填写运行代码

                        PrintMes("[INFO] Starting iMusic system.....",'green')  
                        println('[INFO] Checking environment.....') 
                        println('[INFO] Worksapce: '+ "${MyJenkinsWorkSpace}") 
                        println('[INFO] Production environment: '+"${Prod_env}")
                        println('[INFO] Development environment:'+"${env.Dev_env}")
                        println('[INFO] Git respositories:'+"${GitRespositories}")
                        PrintMes("[Info] Initialization Completed! ",'green')  

                    }
                }
            }
        }

        //下载代码
        stage("GetCode"){ //阶段名称
       //     when { environment name: 'Prod', value: 'Prod' }
            steps{  //步骤
                timeout(time:5, unit:"MINUTES"){   //步骤超时时间
                    script{ //填写运行代码
                        PrintMes("[INFO] Getting codes from Github for iMusic system.....",'green')    
                        mygit = tool "MyGit"
                        println(mygit)
                        sh "pwd" 
                        sh mygit + " --version"
                        PrintMes("[INFO] Github path: git@github.com:BraveSongMan/" + "${github_path}",'green')  
                        sh mygit + " clone git@github.com:BraveSongMan/" + "${github_path}"
                    //    println("${Environment}")
                    //    input id: 'Environment', message: 'Continues? ', ok: 'Yes, go ahead.', parameters: [choice(choices: ['Prod', 'Dev'], description: '', name: 'Environment')], submitter: 'bravesongman,admin'
                }
            }
        }
    }
        //Maven build
        stage("Build"){ //阶段名称
       //     when { environment name: 'Prod', value: 'Prod' }
            steps{  //步骤
                timeout(time:5, unit:"MINUTES"){   //步骤超时时间
                    script{ //填写运行代码
                        PrintMes("[INFO] Building for iMusic system.....",'green')   
                        mvnHome = tool "MyMaven"
                        println(mvnHome)
                        sh "pwd"    
                        sh "cp -r ${env.workspace}/${github_path}/* ${MyJenkinsWorkSpace}"    
                        println("${deploy_script}")
                        sh "${deploy_script}"      
                    //    println("${Environment}")
                    //    input id: 'Environment', message: 'Continues? ', ok: 'Yes, go ahead.', parameters: [choice(choices: ['Prod', 'Dev'], description: '', name: 'Environment')], submitter: 'bravesongman,admin'
                }
            }
        }
    }

}

    //构建后操作
    post {
        always {
            script{
                println("always")
            }
        }

        success {
            script{
                PrintMes("[INFO] Cleaning source codes from workspace",'blue')   
                sh "rm -rf ${github_path}"  
                PrintMes("[INFO] Removal completed",'blue')   
                currentBuild.description = "\n Build Succeed" 
            }
        }

        failure {
            script{
                PrintMes("[INFO] Cleaning source codes from workspace",'blue')   

                sh "rm -rf ${github_path}"  
                PrintMes("[INFO] Removal completed",'blue')  
                currentBuild.description = "\n Build Failure!" 
            }
        }

        aborted {
            script{
                PrintMes("[INFO] Cleaning source codes from workspace",'blue')   
                sh "rm -rf ${github_path}"  
                PrintMes("[INFO] Removal completed",'blue')  
                currentBuild.description = "\n Build Cancel!" 
            }
        }
    }
}
