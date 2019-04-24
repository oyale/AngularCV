<p align="center">
  <img src="https://github.com/StegSchreck/AngularCV/blob/master/src/assets/img/AngularCV.png" width="300px">
</p>

# AngularCV

## Implementación en GitHub Pages
Gracias a [mellab](https://github.com/mellab) por escribir esta documentación.

Después de clonar/bifurcar este proyecto y ajustarlo a sus necesidades (y configurarlo con sus datos), es posible que desee implementarlo para que esté disponible para cualquiera.

Esta es una pequeña guía de cómo implementar este proyecto Angular en [GitHub Pages](https://pages.github.com/) usando la herramienta CLI Angular. Se puede desplegar de otra manera. Esto es sólo una sugerencia.

### Paso 0: Instalar angular-cli-ghpages
```sh
npm i angular-cli-ghpages --save-dev
```

### Paso 1: Compila tu aplicación Angular en `/dist`
```sh
ng build && ng build --prod
```

### Paso 2: Configura tu URL
```sh
ng build --prod --base-href https://stegschreck.github.io/AngularCV/
```

Este comando establecerá su *href base*, que es la URL del sitio en el que se está ejecutando nuestra aplicación. Al crear la URL el formato es el siguiente:
`https:// (tu nombre de GitHub) .github.io/ (tu nombre de repositorio) /``

No olvides la barra al final o no funcionará.

### Paso 3: Desplegar el proyecto con una compilación desde la carpeta `/dist
```sh
npx ngh
```

