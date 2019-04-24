<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV.png" width="300px">
</p>

# AngularCV

Este fork es una traducción al castellano del proyecto <a href="https://github.com/StegSchreck/AngularCV/">AngularCV</a>, creado por Sebastian Schreck.


Este proyecto tiene el propósito de tener un simple y autoalojado CV en línea fuera de LinkedIn, Xing, etc.

Para una demostración en vivo, puede visitar [cv.schreck.berlin](http://cv.schreck.berlin), página para la cual este proyecto fue creado originalmente. 

Puede consultar la demostración en vivo de la versión en castellano [https://oyale.github.io/AngularCV-es/](aquí).


## De terceros 

Utiliza [Angular](https://angular.io/) y los componentes [Material Design components for Angular](https://material.angular.io/).
Además, se incluyen las siguientes fuentes de terceros:
* [Roboto](https://fonts.google.com/specimen/Roboto)
* [Roboto Slab](https://fonts.google.com/specimen/Roboto+Slab)
* [IcoMoon Free](https://icomoon.io/#preview-free)
* [jsPDF](https://github.com/MrRio/jsPDF)


## Configuración

Los elementos del CV para las secciones de experiencia laboral, educación, proyectos y voluntariado se configuran en formato JSON en `/src/app/cv-item/cv-item.data.ts`.
Por favor, tenga en cuenta la organización de la sección de educación que se aplica aquí. Esto le permite reordenar las secciones a su gusto.

A la postre, esto es sólo una prueba de concepto. Siéntase libre de tomarlo como una plantilla y ajustarlo todo a sus necesidades.

#### Añadir sus propios datos

Esto es lo que tendrás que cambiar para adaptar este proyecto para ti.

* `src/app/cv-item/cv-items.data.ts` contiene todos los datos configurables de la página
* Puede utilizar la versión actual como modelo, pero tendrá que sustituir los datos de ejemplo por los suyos propios.
* Hay diferentes casos de uso configurados, por lo que puede ver diferentes conjuntos de datos, que se mostrarán de forma diferente en la página renderizada.
* Algunos campos son obligatorios porque aparecen en la interfaz. Por favor, compruébelo usted mismo
* Recomiendo clasificar los elementos por fecha final descendente, para que el elemento más nuevo se muestre en la parte superior.
* Reemplace `src/assets/img/avatar.jpg` con su foto de perfil (avatar)
* Reemplace `src/assets/img/cover.jpg` con una imagen para su página de resumen, imagínela como una especie de portada de libro.
* Añada las imágenes que necesite en `src/assets/img/item thumbnails` (dependiendo de lo que configure en `src/app/cv-item/cv-items.data.ts`)
* Añada todos los adjuntos que necesite en `src/assets/attachments`  (dependiendo de lo que configure en `src/app/cv-item/cv-items.data.ts`)
* Reemplace `src/favicon.ico`, contiene el icono que su navegador muestra en la barra de tareas.

Para cumplir con la licencia de este proyecto y los elementos de terceros en él, por favor, deje la sección "Acerca de" sin cambios, así como el enlace a la misma en el pie de página.

## Fundamentos de Angular

Consulte la [Guía básica para Angular](ANGULAR.md) para obtener información básica. Para más información, visite el sitio web [Angular](https://angular.io/).

## Implementación para modo de producción

<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV_Deployment.png" width="450px">
</p>

Después de clonar/bifurcar este proyecto y ajustarlo a sus necesidades (y configurarlo con sus datos), es posible que desee implementarlo para que esté disponible para cualquiera.

Vea mi [Guía de implementación de AWS](DEPLOYMENT_ON_AWS.md) para una posible forma de lograr esto con [Amazon Web Services (AWS)](https://aws.amazon.com/).

También puede utilizar [Uberspace](https://uberspace.de/), consulte la [Guía de implementación de Uberspace](DEPLOYMENT_ON_UBERSPACE.md) correspondiente .

Como otra opción, puede utilizar [Github Pages](https://pages.github.com/), consulte la sección correspondiente [Github Pages 
guía de implementación](DEPLOYMENT_ON_GITHUB_PAGES.md).
