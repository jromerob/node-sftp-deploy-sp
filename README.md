## node-sftp-deploy con algunos cambios y traducido al castellano

> Sube y despliega archivos a un servicio SFTPcon usuario y contraseña

Este es una copia de node-sftp-deploy-i con algunos cambios

* Se implementa como clase 
* Emite eventos para un mejor control del proceso
* Los mensajes por defecto se muestran en castellano
* Permite silenciar la consola

## Instalación

```bash
npm install --save node-sftp-deploy-es
```

## Uso

```javascript
Sftp = require('@juancarlosrmr/node-sftp-deploy-sp');
var sftp = new Sftp()

var config = {
    host: "xxxx.xxxxxxxxx.yy",
    port: "22",
    user: "user",
    pass: "password",
    remotePath: "/var/www/carpetaremota",
    sourcePath: "./dist/desa",
    silent: true
};

sftp.on('connected', () => {
    console.info('Recibido evento de conexión');

})

sftp.upload(config)
    .then(data => ftpDeployOK(data))
    .catch(err => ftpDeployKO(err));


function ftpDeployOK(data) {
    console.group();
    console.log('--------------------');
    console.log('✅ SUBIDA FINALIZADA'); 
    console.log('--------------------');
    console.groupEnd
};

function ftpDeployKO(err) {
    console.group();
    console.log('❌ ERROR !!!'); 
    console.log('------------');
    console.log(err); 
    console.groupEnd

}
```

## Objeto de Configuración
La configuracion a usar se pasará a través de un objeto de configuración en el método upload con las siguientes propiedades

| Propiedad  |   | Valor por defecto |
| ------------ | ------------ | ------------ |
|  host | nombre o ip del servidor sftp | |
|  port | Puerto |22
|  user | nombre de usuario ||
|  pass | contraseña | |
|  remotePath | path del servidor ftp donde desplegar | |
|  sourcePath | Carpeta local de código a desplegar |./|
|  remotePlatform | Plataforma remota (linux o windows) |null |
|  includePattern | Patrón de expresión regular para filtrar los archivos que se cargarán. |null|
|  cacheFile | Usar almacenamiento en caché (las hash md5 de los archivos se almacenan localmente y la carga del archivo se omite si se intenta cargar el mismo) . |null|
|  silent | Silenciar salida por consola |null|



## Eventos

| Evento emitido  |   |
| ------------ | ------------ |
|  connected |  Se emite al establecerse la conexión    |
|  error |  Se emite al producirse un error    |
|  con_error | Error en la conexiónr    |
|  con_end |  Conexión finalizada    |
|  con_close |  Conexión cerrada    |
|  file_upload | Se emite al procesar un de archivo. Recibe el objeto con los datos  de la subida:  {  file: filepath, remote: finalRemotePath } | 
|  finish |  Se emite al finalizar el proceso , recibe el parámetro del numoer de archivos procesados    |


## Ejemplo de tarea de despliegue
### Definición de script de despliegue
Para el uso del módulo se propone el siguiente entorno:

Definir un script que realice el despliegue y asignarle un nombre por ejemplo deploy_des para un despliegue de desarrollo. en nuestro caso lo guardamos en la carpeta deploys de la raíz del proyecto.

`deploy_des.js`

    Sftp = require('@juancarlosrmr/node-sftp-deploy-sp');
    var config = {
        host: "tuservidor.sftp.es",
        port: "22", //<- o el puerto que corresponda
        user: "[usuario]",
        pass: "[contraseña]",
        remotePath: "/carpeta/remota",
        sourcePath: "./carpetat/local",
        silent: false
    };
    
	
    var sftp = new Sftp()

    sftp.on('connected', () => { // Ejemplo de suscripcion a evento de conexión
        console.info('¡Conectado !');
    })
    

    sftp.upload(config) // Llamada a método que desencadena la subida
        .then(data => ftpDeployOK(data))
        .catch(err => ftpDeployKO(err));
    
    
    function ftpDeployOK(data) {
        console.log('✅ SUBIDA FINALIZADA');
    };
    
    function ftpDeployKO(err) {
        console.log('❌ ERROR !!!'); 
        console.log(err); 
    
    }

#### A tener en cuenta

**sourcePath**: Es la carpeta del código a desplegar, en ella debemos tener ya generado el código a subir. Si disponemos de distintos entornos podemos configurar la carpea de salida del build para disponer de despliegues generados por entornos que nos puede servir para desarrollar un script de despliegue que nos cubra desplieges a los distintos entornos


### Definición de tarea en package.json

Dentro de la etiqueta scripts del package.json definimos nuestra tarea para lanzar el despliegue:
```json
"scripts": {
        "start": "ng serve",
        "lint": "tslint \"src/**/*.ts\"",
        "test": "ng test --single-run",
        "pree2e": "webdriver-manager update",
        "e2e": "protractor protractor.config.js",
        "deploy_des": "node deploys/deploy_des"
    }
```
### Ejecución de la tarea de despliegue

Ejecutar el comando `npm run deploy_des`
