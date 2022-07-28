def connectionString

pipeline {
    agent any

    stages {
        stage('Подготовка каталогов') {
            steps {
                script {
                    reposPath = "${env.WORKSPACE}\\Repos"
                }
                timestamps {
                    cmd("echo %${reposPath}%")
                    cmd("IF EXIST ${reposPath} (rmdir /Q /S ${reposPath})")
                    cmd("mkdir ${reposPath}\\edt_smoke")
                    cmd("mkdir ${reposPath}\\edt-base")
                    cmd("mkdir ${reposPath}\\edt-cf")
                    cmd("mkdir ${reposPath}\\edt-export")
                }
            }    
        }

        // stage('Подготовка каталогов') {
        //     steps {
        //         script {
        //             reposPath = "C:\\Users\\Sergey\\Documents\\Repos"
        //         }
        //         timestamps {
        //             cmd("IF EXIST ${reposPath} (rmdir /Q /S C:\\Users\\Sergey\\Documents\\Repos)")
        //             cmd("mkdir C:\\Users\\Sergey\\Documents\\Repos\\edt_smoke")
        //             cmd("mkdir C:\\Users\\Sergey\\Documents\\Repos\\edt-base")
        //             cmd("mkdir C:\\Users\\Sergey\\Documents\\Repos\\edt-cf")
        //             cmd("mkdir C:\\Users\\Sergey\\Documents\\Repos\\edt-export")
        //         }
        //     }    
        // }

        stage('Экспорт конфигурации из EDT в XML') {
            steps {
                timestamps{
                    script {
                        edtExport   = "${env.WORKSPACE}\\Repos\\edt-export"
                        edtProject  = "${env.WORKSPACE}\\edt_test"
                        workspace   = "${env.WORKSPACE}"
                        // edtExport   = "C:\\Users\\Sergey\\Documents\\Repos\\edt-export"
                        // edtProject  = "C:\\Users\\Sergey\\git\\edt-vm\\edt_test"
                        // workspace   = "C:\\Users\\Sergey\\git\\edt-vm"
                    }
                    cmd("ring edt workspace export --workspace-location ${workspace} --project ${edtProject} --configuration-files ${edtExport}")
                }
            }
        }

        stage('Создаем пустую файловую базу данных') {
            steps {
                timestamps {
                    script {
                        edtBase = "${env.WORKSPACE}\\Repos\\edt-base"
                        // edtBase = "C:\\Users\\Sergey\\Documents\\Repos\\edt-base"
                    }
                    cmd("${env:PathOf1C} CREATEINFOBASE File=${edtBase}")
                }
            }
        }

        stage('Загружаем xml файлы в конфигурацию') {
            steps {
                timestamps {
                    script {
                        // extraPath = "/LoadConfigFromFiles ${env:EDTExport} /UpdateDBCfg"
                        extraPath = "/LoadConfigFromFiles ${env.WORKSPACE}\\Repos\\edt-export /UpdateDBCfg"
                        edtBase = "${env.WORKSPACE}\\Repos\\edt-base"
                    }
                    cmd("${env:PathOf1C} DESIGNER /F ${edtBase} ${extraPath}")
                }
            }
        }

        stage('Выгрузка конфигурации в файл') {
            steps {
                timestamps {
                    script {
                        edtBase = "${env.WORKSPACE}\\Repos\\edt-base"
                        edtCf = "${env.WORKSPACE}\\Repos\\edt-cf\\1cv8.cf"
                    }
                    cmd("${env:PathOf1C} DESIGNER /F ${edtBase} /DumpCfg ${edtCf}")
                }
            }
        }

        stage('Проверка поведения') {
            steps {
                timestamps {
                    script {
                        connectionString = "/F ${edtBase}"

                    }
                    cmd("vrunner vanessa --vanessasettings ./tools/VBParams.json --workspace . --ibconnection ${connectionString}")
                }
            }
        }
    }
    post {
        always {
            allure includeProperties: false, jdk: '', results: [[path: 'out/allure']]
        }
    }
}

def cmd(command){
    if(isUnix()) {
        sh "${command}"
    } else {
        bat "chcp 65001\n${command}"
    }
}
