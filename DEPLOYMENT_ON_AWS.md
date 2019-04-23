<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV.png" width="300px">
</p>

# AngularCV
<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV_Deployment.png" width="450px">
</p>

## Implementación en entorno de producción
Después de clonar/bifurcar este proyecto y ajustarlo a sus necesidades (y configurarlo con sus datos), es posible que desee implementarlo para que esté disponible para cualquiera.

Esta es una pequeña guía de cómo desplegar este proyecto Angular a AWS usando un CodePipeline de AWS y S3 Bucket. Se puede desplegar de otra manera. Esto es sólo una sugerencia.

### Paso 0: Crear un dominio
Lo más probable es que quieras acceder a la aplicación Angular a través de un dominio apropiado (por ejemplo, `tudominio.tld`) o subdominio (por ejemplo, `cv.tudominio.tld`) en lugar de la url de S3 (por ejemplo, `http://yourbucketname.s3-website.eu-central-1.amazonaws.com/`).
Diríjase a su proveedor de dominio favorito y solicite un nuevo dominio o subdominio para este fin.
Posteriormente ajustará el `CNAME` de ese dominio.

### Paso 1: Crear recursos de AWS

<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV_AWS_CodePipeline.png" width="435px">
</p>

Necesita una cuenta gratuita para [Amazon Web Services (AWS)](https://aws.amazon.com/).

:exclamation: Por favor, tenga en cuenta que los recursos que va a crear en la próxima guía le ocasionarán algunos gastos en su cuenta de AWS.

#### Paso 1a: Crear un S3 Bucket
El *S3 Bucket* alojará tu aplicación.

* Abra la sección [Amazon S3](https://s3.console.aws.amazon.com/s3/home) y haga clic en "Create".
* Nombre el bucket como la url a través de la cual se accederá a él más tarde, por ejemplo, `tudominio.tld` o `cv.tudominio.tld`.
  * Esto depende de lo que hizo en el paso 0 (ver arriba)
* Deje todo lo demás en los valores predefinidos.
* Vaya a las propiedades del *S3 bucket*  que acaba de crear y seleccione "Static Website Hosting".
  * Tanto el documento _index_ como el _error document_ deben establecerse en `index.html`.
* Añada una política de acceso externo (de lo contrario nadie vería su sitio web)
  * Vaya a "Permisos" y haga clic en "Política de Bucket".
  * Pegue la siguiente política en el editor de políticas
 ```json
  {
  	"Versión": "2012-10-17",
  	" Statement": [
  		{
  			"Sid": "2",
  			"Efect": "Allow",
  			" Principal": "*",
  			" Action": "s3: GetObject",
  			"Resource": "arn:aws:s3::::cv.tudominio.tld/*"
  		}
  	]
  }
  ```
  * Ajuste la línea `"Recurso": "arn:aws:s3::::cv.tudominio.tld/*"` con tu dominio

#### Paso 1b: Configure la implementación automatizada con AWS CodePipeline y AWS CodeBuild
Esto creará una *pipeline* automatizado, que implementará cualquier cambio que haga en su repositorio. 

* Actualizar el `buildspec.yml` en el repositorio con el nombre del bucket S3 que has creado previamente.
* En AWS, vaya a la sección " [CodePipeline](https://eu-central-1.console.aws.amazon.com/codepipeline/home) "
* Haga clic en "Create" y seleccione un nombre para su *pipeline*, por ejemplo `AngularCVDeployPipeline`.
* Seleccione la fuente "GitHub" y la rama que desea desplegar (por ejemplo, `master`)
  * Usted tendrá que permitir el acceso de AWS a su GitHub.
* En el siguiente paso, elija "AWS CodeBuild" como proveedor de compilación.
* Seleccione "crear un nuevo proyecto de construcción" y nómbrelo, por ejemplo, `AngularCVBuild`.
* Para la imagen del entorno, usaremos la imagen por defecto (`Usar una imagen gestionada por AWS CodeBuild`)
* Seleccione un sistema operativo - actualmente, la única opción disponible es `Ubuntu`.
* Seleccione `Node.js` como runtime (ya que estamos construyendo una aplicación Angular) con la versión `aws/codebuild/nodejs:7.0.0`.
* Para la especificación de la construcción, queremos usar el ya mencionado `buildspec.yml``.
* Crear un rol para su cuenta, por ejemplo, `code-build-AngularCVBuild-service-role` (puede dejar el valor generado)
* Haga clic en "Guardar proyecto de construcción" y luego en "Siguiente paso".
* Añada los permisos a ese rol para permitir que CodeBuild empuje los artefactos de construcción al *S3 bucket*:
  * Abra la sección "[IAM](https://console.aws.amazon.com/iam/home)" en una nueva pestaña y edite el rol que acaba de crear (`code-build-AngularCVBuild-service-role`)
  * Para mayor comodidad, voy a añadir el acceso completo de S3 a esa función, pero usted sólo debe permitir el acceso a ese *bucket* específico que está utilizando.
    * Vaya a Permisos y haga clic en " Agregar Política ".
    * Agregar `AmazonS3FullAccess`
* Ahora, podemos continuar con el CodePipeline
* En el siguiente paso, seleccionamos `no deployment' como proveedor de despliegue.
  * Los archivos ya están desplegados en S3 mediante la siguiente línea en `buildspec.yml`:

    `    - aws s3 cp dist s3://${S3_BUCKET} --recursive`
    
* Finalmente, tendrá que crear un rol  AWS.
  * Haga clic en "Create" y llámelo por ejemplo `AWS-CodePipeline-Service-AngularCV``.
  * Deje los valores por defecto para los otros campos
* En el siguiente paso puede revisar su configuración
  * Después de crear el Pipeline, éste comenzará a funcionar automáticamente y su aplicación AngularCV se desplegará en su cubo S3.

### Paso 2: Ajusta la configuración de tu dominio
Para el dominio que creaste anteriormente, ajusta la configuración para que apunte al cubo S3
* Ve a la configuración de DNS de tu proveedor de dominio
  * También puede usar [AWS Route53](https://console.aws.amazon.com/route53/home) como DNS, que es fácil de usar, pero eso una historia diferente.


* Añada un registro `CNAME` para el (sub)dominio que desee apuntar a la aplicación AngularCV que apunte a la url S3.
  * e.g. `http://cv.tudominio.tld.s3-website.eu-central-1.amazonaws.com`
* La propagación de la configuración de DNS puede tardar algunos minutos.

### Paso 3: (opcional) Usar HTTPS con distribuciones AWS CloudFront y certificados AWS

<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV_AWS_CloudFront.png" width="435px">
</p>

Puede configurar una distribución [AWS CloudFront](https://console.aws.amazon.com/cloudfront/home) para ofrecer su sitio web Angular con HTTPS.
Durante este proceso, [crearemos un certificado SSL gratuito en AWS](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request.html). 
Este último sólo funcionará si tiene acceso a los correos enviados a una de las direcciones utilizadas por AWS.
Recuerde, que esto es sólo una guía básica. 
Recomiendo leer más sobre todos los recursos de AWS que va a crear para encontrar los ajustes de configuración más adecuados para sus necesidades.

#### Paso 3a: Crear un certificado SSL gratuito en AWS
* Vaya a la sección " [AWS Certificate Manager](https://console.aws.amazon.com/acm/home?region=us-east-1#/)" en la región `US East (N. Virginia)`. 
  * El certificado sólo será adecuado para CloudFront, si se encuentra en la región este de los EE.UU.
* Haga clic en "Comenzar" o "Solicitar un certificado" (dependiendo de si ya tiene uno o no).
* Introduzca los nombres de dominio que necesita, por ejemplo, `tudominio.tld` y `cv.tudominio.tld`.
* Haga clic en "Revisar una solicitud".
* Aceptar la solicitud del correo electrónico que acaba de recibir

#### Paso 3b: Crear la distribución de CloudFront
* Vaya a la sección " [CloudFront](https://console.aws.amazon.com/cloudfront/home)" en AWS
* Haga clic en "Crear distribución" y seleccione "Web" como método de distribución.
* Seleccione el *S3 bucket* que ha creado previamente como " *Origin Domain Name* " (nombre de dominio de origen) de entre las sugerencias que aparecen en la pantalla.
* Puede dejar el "Origin ID" generado.
* Recomiendo elegir "Redirigir HTTP a HTTPS" en "Política de protocolo del espectador".
* Desplácese hacia abajo hasta "Ajustes de distribución".
* Elija la tarifa que mejor se adapte a sus necesidades (cuantas más ubicaciones habilite, mayor será el precio)
* Configure los " Alternative Domain Names (CNAMEs) " a su dominio, por ejemplo `cv.tudominio.tld`.
* Seleccione " Certificado SSL Personalizado " y elija el certificado SSL que ha creado anteriormente.
* Introduzca `index.html` en "Default Root Object" (Objeto raíz predeterminado)
* Deje los otros ajustes como están
* Haga clic en "Crear distribución".
  * puede tardar algunos minutos en crearse
* Por último, deberá ajustar el `CNAME` de su dominio a la URL de CloudFront.

#### Paso 3c: Manejo de los eventos de actualización de página
Si uno de sus visitantes se encuentra, por ejemplo, en la sección "Contactar" de su aplicación AngularCV y actualiza la página, obtendrá un error de CloudFront.
Esto se debe al hecho de que el navegador ahora intenta acceder a la ruta `/contacto`, que no existe.
Para que esto funcione, siga estos pasos:
* Seleccione la distribución CloudFront que acaba de crear y vaya a la pestaña "Páginas de error".
* Haga clic en "Crear respuesta de error personalizada".
* Seleccione el código HTTP `403: Forbidden` en "HTTP Error Code"
* "Personalizar la respuesta de error": sí
* Introduzca `/index.html`en "Ruta de la página de respuesta"
* Código HTTP `200: OK` en "Código de respuesta HTTP".
* Repita esto para el código HTTP `404: Not Found`

Ahora, también los enlaces directos a las secciones (`/experiencia`, `/contacto`, etc.) deberían funcionar como esperas que lo hagan.

#### Referencias
* https://www.linkedin.com/pulse/create-angular-app-using-deploy-aws-suite-andr%C3%A9s-casta%C3%B1o
* https://rynop.com/2017/04/20/howto-serve-angular2-app-from-s3-and-cloudfront-with-free-https/
