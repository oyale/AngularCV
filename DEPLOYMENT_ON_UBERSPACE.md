<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV.png" width="300px">
</p>

# AngularCV

## Implementación en Uberspace
Utilizo también [Uberspace](https://uberspace.de/) con fines personales. Este es un servidor ligero donde puede pagar a voluntad. La recomendación es pagar entre 5 y 10 € al mes, el mínimo es de 1 € al mes. Tienen una buena [wiki] (https://wiki.uberspace.de/start) (pero sólo en alemán por ahora) para la mayoría de las necesidades que puedas tener.

Para implementar AngularCV en Uberspace, deberá realizar los siguientes pasos:

1. Registrar un dominio en algún lugar
1. Registre una cuenta de [Uberspace](https://uberspace.de/)
1. Configure su nuevo dominio en su Uberspace, vea [su wiki](https://wiki.uberspace.de/domain:verwalten)
1. Configurar la nueva versión de nodejs e instalar Angular CLI
    ```sh
    echo 'export PATH=/package/host/localhost/nodejs-8/bin:$PATH' >> ~/.bash_profile # usa la nueva versión de nodejs, ya que ésta no es la predeterminada
    source ~/.bash_profile # aplicar inmediatamente el cambio anterior
    npm instalar @angular/cli
    ```
1. Implemente su aplicación AngularJS
    ```sh
    cd ~
    git clone git@github.com:YoutGithubAccount/AngularCV.git
    cd ~/AngularCV
    ng build -prod --output-path=~/html/ # compilar la aplicación para el entorno de producción y copiar el resultado en el directorio raíz de la web
    ```
    
    (Opcional) **Automatizar el despliegue***.
      
      Para mostrar siempre la versión más reciente, puede añadir una tarea cron para realizar los pasos anteriores
      * Añada las siguientes líneas a `~/bin/deploy_cv.sh`, por ejemplo por `vim ~/bin/deploy_cv.sh`:
        ```sh
        #!/bin/bash
  
        cd ~/AngularCV
        git pull
        source source ~/.bash_profile
        ng build -prod --output-path=~/html/ # compilar la aplicación para el entorno de producción y copiar el resultado en el directorio raíz de la web
        ```
      
      * Haga el script ejecutable vía `chmod +x ~/bin/deploy_cv.s` y añada la siguiente línea al crontab
        ```sh
        # Desplegar AngularCV
        @daily ~/bin/deploy_cv.sh
        ``` 
    
    * ver más aquí sobre el alojamiento de sitios web en el [Uberspace wiki](https://wiki.uberspace.de/start:web)
      
1. Para que los enlaces directos y la actualización de la página del navegador funcionen, añada este contenido al nuevo archivo `~/html/.htaccess/`:
    ```
        RewriteEngine on
        RewriteCond %{REQUEST_FILENAME} -s[OR]
        RewriteCond %{REQUEST_FILENAME} -l[OR]
        RewriteCond %{REQUEST_FILENAME} -d
        RewriteRule ^.*$ -[NC,L]
    
        RewriteRule ^(.*) /index.html[NC,L]
    ```
