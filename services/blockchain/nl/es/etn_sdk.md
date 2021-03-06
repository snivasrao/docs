---

copyright:
years: 2016

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# SDK para Node.js de HFC
{: #etn_sdk}
Última actualización: 07 de octubre de 2016
{: .last-updated}

El SDK de Hyperledger Fabric Client (HFC) permite a los desarrolladores de aplicaciones crear aplicaciones Node.js que interactúan con una red de blockchain. Las aplicaciones de Node.js que optimizan el SDK de HFC pueden utilizarse para realizar las siguientes tareas de red:
{:shortdesc}

* Registrar e inscribir a usuarios de forma segura. Un administrador de aplicaciones web con autorización de `registrador` puede registrar e inscribir dinámicamente a los usuarios que se han autenticado en la aplicación web.
* Enviar transacciones a la red de blockchain (desplegar, invocar y consultar). Todas las transacciones son anónimas, confidenciales y no pueden enlazarse sin la autorización "auditor".
* Almacenar en cualquier ubicación claves privadas y certificados confidenciales, tales como una base de datos fuera de blockchain. Esto requiere implementar una interfaz de almacén de clave-valor simple.

El SDK de HFC proporciona las API, a través de las que las aplicaciones interactúan con la red de blockchain de Hyperledger Fabric. Estas API se han diseñado para dar soporte a dos componentes conectables:

1. El almacén de valores de claves conectables, que se utiliza para recuperar y almacenar claves asociadas con un miembro. El método `chain.setKeyValStore()` altera temporalmente la implementación de almacén de valores de claves basado en archivo. El almacén de valores de claves de cadenas se utiliza para almacenar claves privadas confidenciales, de modo que el acceso debe estar protegido adecuadamente.
2. Servicio de miembros conectables, que se utiliza para registrar e inscribir miembros. El método `chain.setMemberServices()` altera temporalmente la implementación predeterminada en `MemberServices`. Los servicios de miembro implementan Hyperledger Fabric como red de blockchain autorizada, que proporciona anonimato, la capacidad de no asociación de transacciones y la confidencialidad.

Puede incluir el SDK de HFC en la app Node.js utilizando el método fuera de línea o el método npm:
*  Método fuera de línea: primero copia los archivos del árbol de origen de Hyperledger Fabric (https://github.com/hyperledger/fabric/tree/master/sdk/node/lib) en el directorio `/lib` de la app Node.js. A continuación, incluya SDK de HFC en la aplicación añadiendo el siguiente fragmento de código:

```js var hfc = require("./lib/hfc");
```

* Método npm: desde la línea de mandatos, primero instale el SDK de HFC desde npm con el siguiente fragmento de código:

```
npm install hfc@0.5.3
```

A continuación, incluya el SDK de HFC en la aplicación con el siguiente fragmento de código:

```js var hfc = require('hfc');
```  
<br>
## Objetos HFC
{: #objects}

Los siguientes objetos HFC (clases e interfaces) se describen a un alto nivel para guiarle por la jerarquía de objetos:

* La clase de nivel superior es `Chain`, que es la representación de cliente de una red de blockchain. HFC le permite interactuar con varias redes y compartir un único objeto `KeyValStore` y `MemberServices` con varios objetos `Chain`, según sea necesario. Para cada red de blockchain, añadirá uno o más objetos `Peer`, que representan los puntos finales a los que HFC se conecta para someter transacciones.
* HFC utiliza la interfaz `KeyValStore` para almacenar y recuperar todos los datos persistentes. Estos datos incluyen claves privadas, que deben mantenerse seguras. La implementación predeterminada es una versión basada en archivos localizada en la clase `FileKeyValStore`.
* La interfaz `MemberServices` se implementa mediante la interfaz `MemberServicesImpl` y proporciona características relacionadas con la seguridad e identidad tales como la privacidad, la capacidad de no asociación y la confidencialidad. Estos problemas de implementación *eCerts* (certificados de inscripción de miembros)y *tCerts* (certificados de transacciones para cada miembro).
* La clase `Miembro` representa usuarios finales que negocian con la red y otros tipos de miembros tales como los iguales (nodos). Utilice la clase `Miembro`, que interactúa con el objeto `MemberServices`, para *registrar* e *inscribir* miembros y usuarios. También puede desplegar, consultar e invocar código de encadenamiento directamente desde la clase "Miembro" negociando con objetos `Peer`; esta implementación simplemente delega el trabajo en un objeto `TransactionContext` temporal.
* La clase `TransactionContext` implementa el grueso de la lógica de despliegue, invocación y consulta. Cada instancia `TransactionContext` recibe un tCert exclusivo de `MemberServices`, que siempre utiliza para someter transacciones. Para emitir varias transacciones con el mismo tCert, recupere un objeto `TransactionContext` directamente de un objeto Member y emita varias operaciones de despliegue, invocación y consulta. Sin embargo, si se utiliza un único tCert para varias transacciones se enlazan las transacciones de tal modo que pueden identificarse como que incluyen el mismo usuario anónimo. Para evitar el enlace de transacción, llame al despliegue, invocación y consulta en el objeto `User` o `Member`.  

<br>
## Aplicación Node.js de ejemplo
{: #nodesample}

La siguiente aplicación Node.js de ejemplo saca el máximo provecho de las API de SDK de HFC para poder interactuar con una red de blockchain de Bluemix. El programa funciona con los dos planes de red de blockchain (inicial y HSBN), y con cualquier sistema operativo del lado de cliente.

El objetivo es utilizar una aplicación JavaScript--[helloblockchain.js](https://github.com/IBM-Blockchain/SDK-Demo/blob/master/helloblockchain.js)--para desplegar satisfactoriamente código de encadenamiento--[chaincode_example02](https://github.com/IBM-Blockchain/SDK-Demo/blob/master/chaincode_example02.go)--en la red de Bluemix, seguido de una invocación y consulta.  

1. Este programa requiere Node.js y el gestor de paquetes JavaScript npm.  La instalación de la última versión de [Node.js](https://nodejs.org/en/) incluirá automáticamente npm.  

1. Abra un terminal y cree un directorio (espacio de trabajo) donde colocará los módulos de nodo y el código fuente de helloblockchain.js. Por ejemplo:

    ```
    mkdir -p $HOME/workspace
    ```

1. Vaya a la carpeta de espacio de trabajo recién creada e instale HFC v0.5.3 con el siguiente mandato:

     ```
     cd $HOME/workspace
     npm install hfc@0.5.3
     ```

1. Copie el programa [helloblockchain.js](https://github.com/IBM-Blockchain/SDK-Demo/blob/master/helloblockchain.js) y guárdelo en la carpeta de espacio de trabajo.  
   El directorio `/workspace` debe ser parecido a la captura de pantalla que se muestra a continuación:

   ![Espacio de trabajo de nodo](images/nodeworkspace.PNG "Espacio de trabajo de nodo")

1. Si todavía no lo ha hecho, acceda al icono de [Blockchain](https://console.ng.bluemix.net/catalog/services/blockchain/) en Bluemix y crear una instancia del servicio. Seleccione el plan **Desarrollador inicial** o el plan **Red empresarial de alta seguridad** (![](images/green_dot.png) si se aprueba). Pulse el botón **CREATE** y obtenga las **Credenciales de servicio** copiando y pegando el archivo JSON; guárdelo como ServiceCredentials.json en su directorio '/workspace' local. Asegúrese de copiar toda la carga útil JSON; debe tener 202 líneas en un editor estándar.  **Nota**: al ejecutar una instancia de blockchain frente al formato de [nueva consola](https://new-console.ng.bluemix.net/#overview) de Bluemix, observará una diferencia en la salida de las **Credenciales de servicio**.  Concretamente, la línea "credentials" se elimina del objeto.  Para arreglarlo, añada este fragmento de código en la línea 2 del archivo ServiceCredentials.json:

	```
	"credentials": {
	```

1. A continuación, añada un cierre final `}` a la línea 202 para cerrar el objeto. El diseño de ServiceCredentials.json debería duplicar el del ejemplo [ServiceCredentials.json](https://github.com/IBM-Blockchain/SDK-Demo/blob/master/ServiceCredentials.json), dejando una carga útil de 202 líneas. Si obtiene las credenciales de una instancia de blockchain derivada del formato de [consola clásica](https://console.ng.bluemix.net/) de Bluemix, no es necesario preocuparse por esta discrepancia. Las capturas de pantalla siguientes muestran las diferencias en los dos diseños, con la primera mostrando la *nueva consola* y la última mostrando la *clásica*:

     ![Nueva consola de Credenciales de servicio](images/servicecreds1.png "Nueva consola de Credenciales de servicio")

     ![Credenciales de servicio](images/servicecreds.png "Credenciales de servicio")

1. Tras añadir ServiceCredentials.json, el directorio `/workspace` debe parecerse a la siguiente captura de pantalla:

     ![Espacio de trabajo de nodo 2](images/nodeworkspace2.PNG "Espacio de trabajo de nodo 2")

1. Cuando se ejecuta el programa, el SDK de HFC crea el directorio `keyValStore` dentro de $HOME/workspace.  Este directorio `keyValStore` contiene las claves criptográficas para cada usuario inscrito.  No es necesario suprimir el directorio `keyValStore` al conectarse con nuevas redes de Bluemix, en su lugar, se crearán directorios `keyValStore` exclusivos para cada instancia de Bluemix.  

1. Cree un directorio de código de encadenamiento bajo $GOPATH, tal como se muestra a continuación. Si todavía no ha establecido un $GOPATH en la máquina, siga las instrucciones [aquí](https://github.com/golang/go/wiki/GOPGATH).

	```
	mkdir -p $GOPATH/src/chaincode_example02
	```

1. Copie [chaincode_example02.go](https://github.com/IBM-Blockchain/SDK-Demo/blob/master/chaincode_example02.go) en este nuevo directorio - `$GOPATH/src/chaincode_example02`.  Este es el fragmento de chaincode real que se desplegará en la red de Bluemix una vez que se ejecute el programa.  

1. Recupere [vendor.zip](https://github.com/IBM-Blockchain/SDK-Demo/blob/master/vendor.zip) y guárdelo en el mismo directorio - `$GOPATH/src/chaincode_example02`. El paquete vendor.zip contiene bibliotecas y dependencias del codebase de Hyperledger Fabric v0.5. La extracción predeterminada de Windows crea una vía de acceso parecida a:**C:\GOPATH\src\chaincode_example02\vendor**. Antes de realizar la extracción, debe suprimir el directorio `\vendor` de esta vía de acceso, o el despliegue del código de encadenamiento fallará. Una vía de acceso correcta en Windows se parecerá a la siguiente: **C:\GOPATH\src\chaincode_example02\vendor\github.com\hyperledger\fabric**. (Nota: un directorio `\vendor` es correcto.)  La irregularidad de un segundo directorio `\vendor` no se produce en Linux u OS X.

1. Ahora debe tener un directorio dentro de $GOPATH parecido al siguiente ejemplo:

    ![Node $GOPATH](images/nodegopath.PNG "Node $GOPATH")

1. Desde el directorio '/workspace' local, ejecute el programa node:

	```
	node helloblockchain.js -c chaincode_example02
	```
	Habilite los registros de depuración:
	```
	DEBUG=hfc node helloblockchain.js -c chaincode_example02
	```

	Habilite los rastreos gRPC:
	```
	GRPC_TRACE=all DEBUG=hfc node helloblockchain.js -c chaincode_example02
	```

Si las transacciones para `desplegar`, `invocar` y `consultar` son satisfactorias, verá los siguientes mensajes en el terminal:

```
Successfully deployed chaincode: request={"fcn":"init","args":["a","100","b","200"],"certificatePath":"/certs/blockchain-cert.pem","chaincodePath":"github.com/chaincode_example02/"}, response={"uuid":"2d6ad8d6-1390-4c60-a01b-f4c301175eb7","chaincodeID":"9be0a0ed3f1788e8728c8911c747d2f6d0e205fa63422dc598d498fe709b9b8d","result":"TODO: get actual results; waited 120 seconds and assumed deploy was successful"}

Successfully submitted chaincode invoke transaction: request={"chaincodeID":"9be0a0ed3f1788e8728c8911c747d2f6d0e205fa63422dc598d498fe709b9b8d","fcn":"invoke","args":["a","b","1"]}, response={"uuid":"f9a902d2-44d8-4b68-b43d-419470ba73ae"}

Successfully completed chaincode invoke transaction: request={"chaincodeID":"9be0a0ed3f1788e8728c8911c747d2f6d0e205fa63422dc598d498fe709b9b8d","fcn":"invoke","args":["a","b","1"]}, response={"result":"waited 20 seconds and assumed invoke was successful"}

Successfully queried  chaincode function: request={"chaincodeID":"9be0a0ed3f1788e8728c8911c747d2f6d0e205fa63422dc598d498fe709b9b8d","fcn":"query","args":["a"]}, value=99
```

Tenga en cuenta que cuando se ejecuta en una Red de desarrollador inicial algunas veces puede tardar hasta diez minutos para que el contenedor del código de encadenamiento se inicie.  Sin embargo, una vez iniciado, los despliegues y las invocaciones posteriores se ejecutarán inmediatamente porque los archivos de requisito previo se han almacenado en la máquina host para la instancia de blockchain.  

Vaya al separador **Blockchain** desde la **Consola de red**. Esta vista muestra los bloques que se están añadiendo al libro mayor de blockchain cuando el programa helloblockchain.js emite transacciones de despliegue e invocación. La siguiente captura de pantalla muestra los resultados de ejecutar dos veces helloblockchain.js, con los argumentos predeterminados para "a" y "b":

   ![Espacio de trabajo de nodo3](images/nodeworkspace3.PNG "Espacio de trabajo de nodo 3")  

<br>
## Resolución de problemas
Asegúrese de que está ejecutando **hfc@0.5.3** emitiendo cualquiera de los siguientes mandatos desde el directorio **/workspace**:
  * npm list | grep hfc
  * npm list -g | grep hfc  (si se ha instalado utilizando el distintivo global `-g`)

Utilice el siguiente procedimiento se recibe un mensaje de consulta:

  ```
Failed to query chaincode, function: request={"chaincodeID":"9be0a0ed3f1788e8728c8911c747d2f6d0e205fa63422dc598d498fe709b9b8d","fcn":"query","args":["a"]}, error={"error":{"status":"FAILURE","msg":{"type":"Buffer","data":[69,114,114,111,114,58,70,97,105,108,101,100,32,116,111,32,108,97,117,110,99,104,32,99,104,97,105,110,99,111,100,101,32,115,112,101,99,40,112,114,101,109,97,116,117,114,101,32,101,120,101,99,117,116,105,111,110,32,45,32,99,104,97,105,110,99,111,100,101,32,40,57,98,101,48,97,48,101,100,51,102,49,55,56,56,101,56,55,50,56,99,56,57,49,49,99,55,52,55,100,50,102,54,100,48,101,50,48,53,102,97,54,51,52,50,50,100,99,53,57,56,100,52,57,56,102,101,55,48,57,98,57,98,56,100,41,32,105,115,32,98,101,105,110,103,32,108,97,117,110,99,104,101,100,41]}},"msg":"Error:Failed to launch chaincode spec(premature execution - chaincode (9be0a0ed3f1788e8728c8911c747d2f6d0e205fa63422dc598d498fe709b9b8d) is being launched)"}
  ```

Aumente el tiempo de espera de despliegue en la aplicación Node.js. El valor predeterminado está establecido en 60 segundos, pero puede tardar más en que el código se despliegue, compile y empiece su ejecución correctamente en el contenedor de Docker. Intente aumentar el tiempo de espera de despliegue en 120 segundos. Esta idiosincrasia solo se tiene en cuenta en los planes del desarrollador inicial como resultado de los recursos computacionales compartidos en una máquina que aloja la instancia de blockchain:

  ```js
chain.setDeployWaitTime(120);
  ```

Una vez que se ha desplegado el código de encadenamiento satisfactoriamente en la red, puede reducir el tiempo de espera del despliegue a una cantidad nominal, como por ejemplo unos pocos segundos.

Si recibe un error de reconocimiento, pruebe con otra versión de `grpc`. Puede acceder a la versión de grpc con cualquiera de los siguientes mandatos:
    - `npm list | grep grpc`
    - `npm list -g | grep grpc`  

<br>
## Claves públicas y privadas
{: #keys}

Hyperledger Fabric utiliza entidades emisoras de certificados y sus claves públicas y privadas subyacentes, para satisfacer los requisitos de seguridad de las empresas que operan en un blockchain compartido. La gestión de identidades de miembros, la gestión de roles y la privacidad transaccional pueden controlarse a través del SDK de HFC.

La privacidad de usuario y transaccional en un blockchain compartido se gestionan a través de la implementación de una infraestructura de claves públicas (PKI). La PKI, a través de entidades emisoras de certificados, gestiona la generación, distribución y revocación de claves y certificados digitales. Las especificaciones técnicas completas para PKI y los Servicios de pertenencia se describen en la sección de seguridad de Hyperledger Fabric v0.5 [especificación de protocolo](https://github.com/hyperledger/fabric/blob/master/docs/protocol-spec.md). A continuación se explican los principios básicos de PKI de Hyperledger Fabric:

1. La autoridad de registro (RA) valida la identidad de un usuario que está solicitando acceso a la red de blockchain.  Esto puede llevarse a cabo dinámicamente por un usuario con autorización de `registrador`, o manualmente editando el archivo membersrvc.yaml. El proceso de registro se produce fuera de banda y se ejecuta a través de la función `RegisterUser`. La RA asigna credenciales de inscripción-`<enrollID>` y `<enrollPWD>`-al usuario.

2. El usuario a continuación enviará una solicitud de inscripción a la autoridad de certificados de inscripción (ECA) utilizando la función `CreateCertificatePair`. Esta carga útil contiene el `<enrollPWD>`, la clave de verificación de firma pública y la clave de cifrado pública de una vez del usuario, y está firmada con la clave de verificación de firma privada del usuario. <br><br>Al recibir la solicitud de inscripción, la ECA emite una solicitud encriptada al usuario que sólo puede descifrarse con la clave de cifrado privada del usuario. Después de descifrar la solicitud, el usuario reenvía la solicitud de certificado. La ECA, supeditada a una respuesta descifrada correctamente, devuelve un par de certificado autenticado firmado con su firma digital. <br><br>La firma digital se genera troceando criptográficamente la solicitud de certificado (mensaje) utilizando el algoritmo SHA-2 para producir un "resumen". Este "resumen de mensaje" luego se cifrará con la clave de firma privada de la ECA. Los miembros de red entonces podrán autenticar la firma digital descifrándola con la clave de firma pública de la ECA. El par de certificado de inscripción devuelto (eCert) contiene un certificado para la firma de datos (privado) y otro para el cifrado de datos (público). Este par de eCert es estático y a largo plazo y puede ser visible o invisible a las transacciones.

3. Para realizar operaciones en cualquier red de blockchain, cada usuario debe tener también certificados de transacción (tCerts). Tras la inscripción satisfactoria, un usuario somete una solicitud a la entidad emisora de certificados de transacciones (TCA) para solicitar un lote de tCerts. Un tCert tiene una duración corta y es específico para una transacción, y el cliente puede modificarlo utilizando una API. Después de verificar el eCert del usuario, el TCA asigna un lote de tCerts y una KeyDF_Key (Clave de función de derivación de claves), que permite al usuario descifrar sus claves privadas. Mientras que la única KeyDF_Key se utiliza para cada tCert en el lote, la clave privada subsiguiente que se genera es exclusiva para cada tCert. Para realizar operaciones, un cliente debe poder firmar la carga útil de transacciones con la clave privada descifrada. Sólo entonces se reenviará una transacción a los iguales de validación de red para el consenso.
