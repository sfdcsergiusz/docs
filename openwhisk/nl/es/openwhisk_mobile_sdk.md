---

copyright:
  years: 2016

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Uso del SDK móvil de {{site.data.keyword.openwhisk_short}}
{: #openwhisk_mobile_sdk}
Última actualización: 26 de agosto de 2016
{: .last-updated}

{{site.data.keyword.openwhisk}} proporciona un SDK móvil para dispositivos iOS y watchOS 2 que permite a las apps activar
fácilmente desencadenantes remotos e invocar acciones remotas. Actualmente no hay disponible una versión para Android;
los desarrolladores de Android pueden utilizar directamente la API REST de {{site.data.keyword.openwhisk}}.
{: shortdesc}

El SDK móvil se escribe en Swift 2.2, y admite iOS 9 y releases posteriores.

## Añadir el SDK a su app
{: #openwhisk_add_sdk}

Puede instalar el SDK móvil usando CocoaPods, Carthage o desde el directorio de origen.

### Instalación usando CocoaPods 
{: #openwhisk_add_sdk_cocoapods}

El SDK de {{site.data.keyword.openwhisk_short}} para móvil está disponible para distribución pública por medio de CocoaPods. Suponiendo que CocoaPods esté instalado, ponga las líneas siguientes en un archivo llamado 'Podfile' dentro del directorio de proyecto de la app starter. 

```
install! 'cocoapods', :deterministic_uuids => false
use_frameworks!

target 'MyApp' do
     pod 'OpenWhisk', :git => 'https://github.com/openwhisk/openwhisk-client-swift.git', :tag => '0.1.7'
end

target 'MyApp WatchKit Extension' do 
     pod 'OpenWhisk', :git => 'https://github.com/openwhisk/openwhisk-client-swift.git', :tag => '0.1.7'
end
```
{: codeblock}

En la línea de mandatos, escriba `pod install`. Este mandato instala el SDK para una app de iOS con una extensión watchOS 2.  Utilice el archivo de espacio de trabajo que crea CocoaPods para su app para abrir el proyecto en Xcode.

### Instalación usando Carthage
{: #openwhisk_add_sdk_carthage}

Cree un archivo en el directorio del proyecto de la app y llámelo 'Cartfile'. Añada la línea siguiente al archivo:
```
github "openwhisk/openwhisk-client-swift.git" ~> 0.1.7 # Or latest version
```
{: codeblock}

En la línea de mandatos, escriba `carthage update --platform ios`. Carthage descarga y construye el SDK, crea un directorio llamado
Carthage en el directorio de proyecto de su app y coloca un archivo OpenWhisk.framework dentro de Carthage/build/iOS.

Luego debe añadir OpenWhisk.framework
a las infraestructuras incluidas en su proyecto Xcode.

### Instalación a partir de código fuente
{: #openwhisk_add_sdk_source}

El código fuente está disponible en https://github.com/openwhisk/openwhisk-client-swift.git.
Abra el proyecto utilizando `OpenWhisk.xcodeproj` con Xcode.
El proyecto contiene dos esquemas: "OpenWhisk" (destinado a iOS) y "OpenWhiskWatch" (destinado a watchOS 2).
Compile el proyecto
para los destinos que necesite y añada las infraestructuras resultantes a su app (normalmente en
~/Library/Developer/Xcode/DerivedData/nombre_app).

## Instalación del ejemplo de app starter
{: #openwhisk_install_sdkstart}

A utilizar la CLI de {{site.data.keyword.openwhisk_short}} para descargar código de ejemplo que incluya la infraestructura de
SDK de {{site.data.keyword.openwhisk_short}}.  

Para instalar el ejemplo de app starter, especifique el mandato siguiente:
```
wsk sdk install iOS
```
{: pre}

Este mandato descarga un archivo comprimido que contiene la app de iniciador. Dentro del directorio de proyecto hay un podfile. 

Para instalar el SDK, especifique el mandato siguiente:
```
pod install
```
{: pre} 

## Iniciación con el SDK
{: #openwhisk_sdk_getstart}

Para empezar rápidamente, cree un objeto WhiskCredentials con sus credenciales de API de {{site.data.keyword.openwhisk_short}}
y cree una instancia de {{site.data.keyword.openwhisk_short}} a partir del objeto.

Por ejemplo, en Swift 2.1, utilice el código de ejemplo siguiente para crear un objeto de credenciales:

```
let credentialsConfiguration = WhiskCredentials(accessKey: "myKey", accessToken: "myToken")

let whisk = Whisk(credentials: credentialsConfiguration!)
```
{: codeblock}

En el ejemplo anterior, pasará los elementos `myKey` y `myToken` que obtiene de {{site.data.keyword.openwhisk_short}}. Puede recuperar la clave y la señal con el mandato de CLI siguiente:

```
wsk property get --auth
```
{: pre}
```
whisk auth        kkkkkkkk-kkkk-kkkk-kkkk-kkkkkkkkkkkk:tttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttttt
```
{: screen}

La serie anterior a los dos puntos es la clave y la serie posterior a los dos puntos es la señal.

## Invocación de una acción de {{site.data.keyword.openwhisk_short}}
{: #openwhisk_sdk_invoke}


Para invocar una acción remota, puede llamar `invokeAction` con el nombre de acción. Puede especificar el espacio de nombres al
que pertenece la acción, o dejarlo en blanco para aceptar el espacio de nombres predeterminado. Utilice un diccionario para pasar parámetros a la acción,
según sea necesario.

Por ejemplo:

```
// En este ejemplo, invocamos una acción para imprimir un mensaje en la consola de OpenWhisk
var params = Dictionary<String, String>()
params["payload"] = "Hi from mobile"

do {
    try whisk.invokeAction(name: "helloConsole", package: "mypackage", namespace: "mynamespace", parameters: params, hasResult: false, callback: {(reply, error) -> Void in
        if let error = error {
            //do something
            print("Error invoking action \(error.localizedDescription)")
        } else {
            print("Action invoked!")
        }

    })
} catch {
    print("Error \(error)")
}
```
{: codeblock}

En el ejemplo anterior, se invoca la acción `helloConsole` usando el espacio de nombres predeterminado.

## Activación de un desencadenante de {{site.data.keyword.openwhisk_short}}
{: #openwhisk_sdk_fire}

Para activar un desencadenante remoto, puede llamar al método `fireTrigger`. Pase los parámetros según
sea necesario, mediante un diccionario.

```
// En este ejemplo, activamos un desencadenante cuando nuestra ubicación ha cambiado por una cantidad determinada

var locationParams = Dictionary<String, String>()
locationParams["payload"] = "{\"lat\":41.27093, \"lon\":-73.77763}"

do {
    try whisk.fireTrigger(name: "locationChanged", package: "mypackage", namespace: "mynamespace", parameters: locationParams, callback: {(reply, error) -> Void in

        if let error = error {
            print("Error firing trigger \(error.localizedDescription)")
        } else {
            print("Trigger fired!")
        }
    })
} catch {
    print("Error \(error)")
}
```
{: codeblock}

En el ejemplo anterior, se activa el desencadenante `locationChanged`.

## Uso de acciones que devuelven un resultado
{: #openwhisk_sdk_actionresult}

Si la acción devuelve un resultado, establezca hasResult en true en la llamada a invokeAction. El resultado de la acción
se devuelve en el diccionario de respuesta, por ejemplo:

```
do {
    try whisk.invokeAction(name: "actionWithResult", package: "mypackage", namespace: "mynamespace", parameters: params, hasResult: true, callback: {(reply, error) -> Void in

        if let error = error {
            //do something
            print("Error invoking action \(error.localizedDescription)")

        } else {
            var result = reply["result"]
            print("Got result \(result)")
        }


    })
} catch {
    print("Error \(error)")
}
```
{: codeblock}

De forma predeterminada, el SDK solo devuelve el ID de activación y cualquier resultado producido por la acción invocada. Para obtener metadatos
del objeto de respuesta completo, que incluye el código de estado de la respuesta HTTP,
utilice el siguiente valor:

```
whisk.verboseReplies = true
```
{: codeblock}

## Configuración del SDK
{: #openwhisk_sdk_configure}

Puede configurar el SDK para que funcione con distintas instalaciones de {{site.data.keyword.openwhisk_short}} usando el parámetro
baseURL. Por ejemplo:

```
whisk.baseURL = "http://localhost:8080"
```
{: codeblock}

En este ejemplo, se utiliza una instalación que se ejecuta en localhost:8080. Si no especifica baseUrl, el SDK móvil usa
la instancia en ejecución en https://openwhisk.ng.bluemix.net.

Puede pasar una sesión NSURLSession personalizada en caso de que necesite gestión especial de red. Por ejemplo, podría tener su propia
instalación {{site.data.keyword.openwhisk_short}} que utilice certificados autofirmados:

```
// crear un delegado de red que confíe en todo
class NetworkUtilsDelegate: NSObject, NSURLSessionDelegate {
    func URLSession(session: NSURLSession, didReceiveChallenge challenge: NSURLAuthenticationChallenge, completionHandler: (NSURLSessionAuthChallengeDisposition, NSURLCredential?) -> Void) {
        completionHandler(NSURLSessionAuthChallengeDisposition.UseCredential, NSURLCredential(forTrust: challenge.protectionSpace.serverTrust!))
    }
}

// crear una sesión NSURLSession que utiliza el delegado de confianza
let session = NSURLSession(configuration: NSURLSessionConfiguration.defaultSessionConfiguration(), delegate: NetworkUtilsDelegate(), delegateQueue:NSOperationQueue.mainQueue())

// establecer el SDK para que utilice esta sesión urlSession en lugar de la compartida predeterminada
whisk.urlSession = session
```
{: codeblock}

### Soporte para nombres calificados
{: #openwhisk_sdk_configure_qual}

Todas las acciones y desencadenantes tienen un nombre completo que se compone se un espacio de nombres, un paquete y una
acción o nombre de desencadenante. El SDK puede aceptar estos elementos como parámetros cuando invoca una acción o activa un desencadenante. El SDK
también proporciona una función que acepta un nombre completo parecido a `/mynamespace/mypackage/nameOfActionOrTrigger`. La
serie del nombre calificado tiene soporte para valores predeterminados sin nombre para espacios de nombres y paquetes que
tienen todos los usuarios de {{site.data.keyword.openwhisk_short}}, por lo que se aplican las reglas de análisis siguientes:

- qName = "foo" results in namespace = default, package = default, action/trrigger = "foo"
- qName = "mypackage/foo" results in namespace = default, package = mypackage, action/trigger = "foo"
- qName = "/mynamespace/foo" results in namespace = mynamespace, package = default, action/trigger = "foo"
- qName = "/mynamespace/mypackage/foo results in namespace = mynamespace, package = mypackage, action/trigger = "foo"

El resto de combinaciones emiten un error WhiskError.QualifiedName. Por lo tanto, cuando utilice nombres calificados, debe envolver la
llamada en un constructor "`do/try/catch`".

### Botón SDK
{: #openwhisk_sdk_configure_button}

Por comodidad, el SDK incluye un `WhiskButton`, que amplía `UIButton` para permitirle invocar acciones.  Para utilizar este `WhiskButton`, siga este ejemplo:

```
var whiskButton = WhiskButton(frame: CGRectMake(0,0,20,20))

whiskButton.setupWhiskAction("helloConsole", package: "mypackage", namespace: "_", credentials: credentialsConfiguration!, hasResult: false, parameters: nil, urlSession: nil)

let myParams = ["name":"value"]

// Llamar esta sección cuando se detecte el suceso de pulsar, p.e., en una IBAction, para invocar la acción
whiskButton.invokeAction(parameters: myParams, callback: { reply, error in
    if let error = error {
        print("Oh no, error: \(error)")
    } else {
        print("Success: \(reply)")
    }
})

// o también puede configurar un botón "autocontenido" que atiende en espera de sucesos de pulsación en sí mismo e invoca una acción

var whiskButtonSelfContained = WhiskButton(frame: CGRectMake(0,0,20,20))
whiskButtonSelfContained.listenForPressEvents = true
do {

   // usar la API de nombre calificado que precisa de do/try/catch
   try whiskButtonSelfContained.setupWhiskAction("mypackage/helloConsole", credentials: credentialsConfiguration!, hasResult: false, parameters: nil, urlSession: nil)
   whiskButtonSelfContained.actionButtonCallback = { reply, error in

       if let error = error {
           print("Oh no, error: \(error)")
       } else {
           print("Success: \(reply)")
       }
   }
} catch {
   print("Error setting up button \(error)")
}
```
{: codeblock}
