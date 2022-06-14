pipeline {
    agent any

    stages {
        stage('Подготовка каталогов') {
            steps {
                timestamps {
                    cmd("IF EXIST ""C:\Users\Sergey\Documents\Repos"" (rmdir /Q /S C:\Users\Sergey\Documents\Repos)")
                    cmd("mkdir C:\Users\Sergey\Documents\Repos\edt_smoke")
                    cmd("mkdir C:\Users\Sergey\Documents\Repos\edt-base")
                    cmd("mkdir C:\Users\Sergey\Documents\Repos\edt_cf")
                    cmd("mkdir C:\Users\Sergey\Documents\Repos\edt-export")
                }
            }    
        }

        stage('Экспорт конфигурации из EDT в XML') {
            steps {
                timestamps{
                    script {
                        edtExport   = "C:\\Users\\Sergey\\Documents\\Repos\\edt-export"
                        edtProject  = "C:\\Users\\Sergey\\git\\edt-vm\\edt_test"
                        workspace   = "C:\\Users\\Sergey\\git\\edt-vm"
                    }
                    cmd("ring edt workspace export --workspace-location ${workspace} --project ${edtProject} --configuration-files ${edtExport}")
                }
            }
        }

        stage('Создаем пустую файловую базу данных') {
            steps {
                timestamps {
                    script {
                        edtBase = "C:\\Users\\Sergey\\Documents\\Repos\\edt-base"
                    }
                    cmd("${env:PathOf1C} CREATEINFOBASE File=${edtBase}")
                }
            }
        }

        stage('Загружаем xml файлы в конфигурацию') {
            steps {
                timestamps {
                    script {
                        extraPath = "/LoadConfigFromFiles ${env:EDTExport} /UpdateDBCfg"
                    }
                    cmd("${env:PathOf1C} DESIGNER /F ${env:EDTBase} ${extraPath}")
                }
            }
        }

        stage('Выгрузка конфигурации в файл') {
            steps {
                timestamps {
                    cmd("${env:PathOf1C} DESIGNER /F ${env:EDTBase} /DumpCfg ${env:EDTCF}")
                }
            }
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
