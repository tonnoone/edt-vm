pipeline {
    agent any

    stages {
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
    }
}

def cmd(command){
    if(isUnix()) {
        sh "${command}"
    } else {
        bat "chcp 65001\n${command}"
    }
}
