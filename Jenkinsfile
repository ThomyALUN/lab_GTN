pipeline {
    agent { label 'web_lab' }

    parameters {
        string(name: 'NAME_CONTAINER', defaultValue: 'sitio_web',
               description: 'Nombre del container')
        string(name: 'IMAGE',         defaultValue: 'php',
               description: 'Nombre de la imagen')
        string(name: 'TAG',           defaultValue: '7.4-apache',
               description: 'Etiqueta / versión de la imagen')
        string(name: 'PORT',          defaultValue: '80',
               description: 'Puerto expuesto')
    }

    stages {

        stage('detener/limpiar') {
            steps {
                script {
                    // ¿Existe algún contenedor con ese nombre?
                    def id = sh(script: "sudo docker ps -aq -f name=${params.NAME_CONTAINER}",
                                returnStdout: true).trim()

                    if (id) {
                        sh "sudo docker stop ${id}"
                        sh "sudo docker rm   ${id}"
                    } else {
                        echo "No hay contenedor ${params.NAME_CONTAINER} en ejecución."
                    }

                    // Limpieza: nunca abortar si algo falla
                    sh "sudo docker system prune -f || true"
                    sh "sudo docker image  prune -af || true"
                }
            }
        }

        stage('run/build') {
            steps {
                // Detener y eliminar contenedores huérfanos si los hubiera
                sh 'sudo docker compose down --remove-orphans || true'
                // Levantar la aplicación
                sh 'sudo docker compose up -d --build'
            }
        }

        stage('verification') {
            steps {
                sh 'sudo docker ps -a'
            }
        }
    }
}

