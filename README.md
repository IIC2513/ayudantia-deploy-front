# Pasos para deployar tu frontend (Por si no te quedó claro con el video)

## Crea tu aplicación de React

En primer lugar, tienes que crear tu aplicación y correrla. Para ello debes correr el siguiente comando en el directorio principal de tu repositorio de GitHub:

`npx create-react-app .`

Luego, para correr la aplicación:

`npm start`

## Buildea la aplicación

Para hacer esto se debe correr el comando:

`npm run build`

Luego de haberlo ejecutado, se debe haber creado una carpeta que se llama *build*.

## Crea el archivo netlify.toml

Para este paso, se debe crear un archivo *netlify.toml* que se encuentre en el directorio principal del repositorio. Este debe contener lo siguiente:

```
[build]
command = "npm run build"
publish = "build"
```

## Realizar el setup del workflow de node.js

Al terminar los pasos previos, es importante que hagas push a los cambios que realizaste previamente. Después, entras a la página del repositorio y accedes a actions. Posteriormente, debes buscar node.js, realizar la configuración y commitear los cambios. Luego debes hacer `git pull` de los cambios en que realizaste del workflow de node.js.

## Login en Netlify y creación de página

En este paso vinculas tu cuenta de GitHub con Netlify y creas una página usando y arrastrando la carpeta `./build` que se encuentra en el directorio principal del repositorio.

## Obtención de Secrets

En este paso debes crear un token en "User Settings" -> "Applications" -> "Personal access token" en la página de Netlify. Recordar que la duración del token debe ser de por lo menos la duración del curso.

Posteriormente, se debe incluir en los secrets de git, con el nombre de `NETLIFY_AUTH_TOKEN`. Esto se puede hacer en "Repositorio de GitHub" -> "Settings" -> "Secrets and variables" -> "Actions" -> "Repository secrets".

De una manera similar se debe recuperar el id de la página de Netlify. Para ello, se ingresa a "Sites" -> "Nombre de tu sitio" -> "Site configuration" -> "Site details" -> "Site ID".

Finalmente, se incluye en los secrets del repositorio en GitHub, con el nombre `NETLIFY_SITE_ID`.

## Cambio del archivo node.js.yml

El archivo yml que les entrega GitHub se ve de la siguiente forma:

```
name: Node.js CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: npm run build --if-present
    - run: npm test
```

Para que el workflow funcione, debe ser cambiado por lo siguiente:

```
name: Node.js CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: npm run build --if-present
    - run: npm test

    - name: Netlify Deploy

      env:
        NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
        NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
      run: netlify deploy --prod
```

Finalmente, se debe realizar un commit de los cambios y el deployment del frontend debería estar listo.

Ayudantía realizada por @PSepulvedaS