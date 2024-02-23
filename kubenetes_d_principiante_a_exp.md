# Kubernetes, de principiante a experto

## **Índice**   

0.  [Notas acerca de estas notas](#id0)  
1. [¿Qué es Kubernetes?](#id1)  
   1.1 [¿Qué se puede hacer con Kubernetes?](#id1.1)
2. [Arquitectura](#id2)  
   2.1 [Arquitectura Master-Node](#id2.1)  
   2.2 [Kubernetes API](#id2.2)  
   2.3 [Kube-Scheduler - Eligiendo nodos para hostear pods](#id2.3)  
   2.4 [Kube-Controler - Conoce los tipos de controladores k8s](#id2.4)  
   2.5 [Etcd - Base de datos de Kubernetes](#id2.5)  
   2.6 [Kubelet - El agente de K8s](#id2.6)  
   2.7 [Kube-Proxy - Un servicio muy importante](#id2.7)  
   2.8 [Container Runtime - ¡De nuevo Docker!](#id2.8)  
   2.9 [Conclusión de la Arquitectura](#id2.9)
3. [Minikube](#id3)  
   3.1 [Instalación de Minikube Kubectl](#id3.1)  
   3.1 [Instalación Docker/VirtualBox](#id3.2)  
   3.2 [Instalación Minikube](#id3.3)  
   3.3 [Instalación Minikube en Windows](#id3.4)  
   3.4 [Comandos básicos de Minikube](#id3.5)  
4. [Pods en Kubernetes VS Contenedores Docker](#id4)  
   4.1 [¿Cómo funciona un contenedor?](#4.1)  
   4.1 [¿Cómo se crea un pod detrás de las escenas?](#id4.2)  
5. [Explorando pods](#id5)  
   5.1 [Crea tu primer pod](#id5.1)  
   5.2 [Describir un pod ](#id5.2)  
   5.3 [Eliminar un pod](#id5.3)  
   5.4 [Obtener el manifiesto de un pod](#id5.4)  
   5.5 [¿No puedes ver la IP de tu pod?](#id5.5)  
   5.6 [Obtener la ip de un pod](#id5.6)  
   5.7 [Ingreso a un contenedor dentro del pod](#id5.7)  
   5.8 [logs](#id5.8)  
   5.9 [Acercamiento a manifiestos](#id5.9)  
    5.9.1 [Crear dos contenedores en el mismo pod](#id5.9.1)  
    5.9.2 [Cómo ingresar al shell de un contendor en el pod](#id5.9.2)  
  5.10 [Labels y Pods](#id5.10)  
    5.10.0 [filtrar pod por labels](#id5.10.1)  
  5.11 ["Problemas" de los pods](#id5.11)  
6. [RéplicaSet](#id6)  
  6.1 [Mi primer RéplicaSet](#id6.1)  
  6.2 [Cómo se relaciona un ReplicaSet con Pods](#id6.2)  
  6.3 [Adopción de pods desde ReplicaSet](#id6.3)  
  6.4 [Problemas de los ReplicaSet](#id6.4)  
7. [Deployments](#id7)  
   7.1 [¿Qué es un Deployment](#id7.1)  
   7.2 [Tu primer Deployment](#id7.2)  
   7.3 [Owner References - Deployment, ReplicaSet y Pods](#id7.3)  
   7.4 [Rolling updates -Actualiza la versión de tu aplicación](#id7.4)  
   7.5 [Histórico  y revisiones de despliegues](#id7.5)  
   7.6 [Change-Cause - ¿Cambiaste algo?](#id7.6)  
   7.7 [Roll back - Si algo salió mal, ¡Regresa a la versión anterior!](#id7.7)  
8. [Servicios y EndPoints](#id8)
   8.1 [¿Qupe es un servicio](#id8.1)  
   8.2 [¿Qué son y para que sirven los endpoints](#id8.2)  
   8.3 [Crea tu primer servicio](#id8.3)  
   8.4 [Describe tu servicio y encuentra información útil](#id8.4)  
   8.5 [Pods y EndPoints](#id8.5)  
   8.6 [Servicios y DNS](#id8.6)  
   8.7 [Servicio de tipo ClusterIP](#id8.7)  
   8.8 [Servicio de tipo NodePort](#id8.8)  
   8.9 [Servicio de tipo LoadBalancer](#id8.9)  
  
9. [Golang, JavaScript y Kubernetes. Aprende a construir aplicaciones reales](#id9)  
   9.1 [Golang: Empieza a escribir tu API](#id9.1)  
   9.2 [Golang: últimos detalles](#id9.2)  
   9.3 [Crea un Dockerfile para tu aplicación en Golang](#id8.3)  
   9.4 [¿No puedes construir un Dockerfile porque no tienes Docker instalado?](#id9.4)  
   9.5 [Escribe manifiestos de Kubernetes para desplegar tu aplicación](#id9.5)  
   9.6 [Aprender a consumir el servicio que creaste](#id9.6)  
   9.7 [Nota: ¿No puedes ver el servicio Backend?](#id9.7)  
   9.8 [Empieza a escribir el  cliente JavaScript que consumirá tu Backend en Go](#id9.8)  
   9.9 [Despliega una nueva version de tu Backend para resolver errores en el FrontEnd](#id9.9)  
   9.10 [Valida que tu servicio Front esté funcionando como debería](#id9.10)  
   9.11 [Crea manifiestos de k8s para desplegar tu servicio Front](#id9.11)  
   9.12 [Crea un Dockerfile para tu aplicación en JavaScript](#id9.12)  
   9.13 [Tip: ¿No puedes construir porque no tienes Docker instalado?](#id9.13)  
   9.14 [Despliega los servicios y valida su funcionamiento](#id9.14)  



7. [¿Qué se puede hacer con Kubernetes?](#id7)  

499. [Apendice](#Apendice)  
500. [Glosario](#glos)  
501. [kubectl get pods](#getpods)  
502. [kubectl run](#run)  
503. [api-resources](#api-resources)  


##  Notas acerca de estas notas <a name="#id0"></a>

Estas son mis notas personales del cuso: **Kubernetes, de principiante a experto** en Udemy.

_Hector Alfredo Alvarez C._  
_halvarez@pasteursoft.com_
## 1 ¿Qué es Kubernetes? <a name="id1"></a>

Es una herramienta opensourse extensible para gestionar cargas de trabajo, y facilita la administración de contenedores. Tiene un ecosistema grande y de rápido crecimiento. Puede manejar con varios runtime de contenedores, pero en este curso se basará en docker.

Kubernetes se puede abrebiar como **_k8s_** porque hay 8 letras entre k y la s.

### 1.1. ¿Qué se puede hacer con Kubernetes? <a name="#id2" </a>

* Service discovery. Autodescubre los contenedores para ofrecer los servicios.
* Rollouts y Rollbacks sencilla.
* Optimización de recursos. Administra los recursos entre nodos y contenedores para ajustar automáticamente los recursos de mejor manera.
* Selfhealing. Crea un contenedor nuevo, cuando uno ha caído y mantiene el estado deseado del deployment.Es decir Remplazo automático de contenedores en caso de falla.
* Escalamiento horizontal sencillo.


## 2. Arquitectura<a name="#id2"> </a>

A groso modo Kubernetes se divide en dos partes: Master y Nodos.

El Master se comunica con los nodos y es como el cerebro donde se controla todo.

Los nodos son en donde se despliegan los contenedores.

Nota: No voy a escribir mucho en este capítulo, solo voy a dejar notas breves.

### 2.1 Arquitectura Master-Node<a name="#id2.1"> </a>

El Maestro es el que dirige y administra las operaciones, mientras que los nodos van a ejecutar los contenedores que son ordenados y administrados desde el master.

Puede haber varios master.

### 2.2 Kubernetes API <a name="#id2.2"> </a>

Esta interface de comunicación sirve para comunicarnos con Kubernetes. La comunicación se realizará con un terminal **_kubectl_** que conecta con la API que interpreta los comandos y genera órdenes para realizar una tarea. Esta comunicación puede ser a travez de un archivo con instrucciones o a travez de comandos.

### 2.3 Kube-Scheduler - Eligiendo nodos para hostear pods <a name="#id2.3"> </a>

El scheduler organiza los nodos necesarios para realizar un despliegue, revisa también que cuenten con los elementos necesarios y determina en dónde debería de desplegar una aplicación.

### 2.4 Kube-Controler - Conoce los tipos de controladores k8s <a name="#id2.4"> </a>

El controlador dentro de si tiene otros elementos:  
**_Node Controller_** Se encarga de verificar el estado de las instancias, si una se cae, que siempre exista otra.  
**_Replication Controller_** Se encarga de todo lo que sea réplica.  
**_End Point Controller_** Se encarga de los servicios entre nodos, cómo las redes por donde se comunican  
**_Service Account y Tokens_Controller_** Se encargan de los temas de autenticación cuando se solicita algo desde el cliente o se requiere algún tipo de autentificación.

### 2.5 Etcd - Base de datos de Kubernetes <a name="#id2.5"> </a>

Dentro del master se encuentra el Etcd que es una base de datos donde se almacena el estado generál del cluster. Ahí se guarda todo lo que va ocurriendo en el cluster. Esta base de datos es muy importante para Kubernetes.

### 2.6 Kubelet - El agente de K8s <a name="#id2.6"> </a>

En cada nodo existe Kubelet que es el responsable de recibir las órdenes desde el master y envíar información al master. 

Cuando se reciben las órdenes se ejecutan y se realiza el despliegue solicitado en el nodo.

Funciona como puerta de comunicación del nodo al maestro.

### 2.7 Kube-Proxy - Un servicio muy importante<a name="#id2.7"> </a>

Kube-Proxy corre en cada nodo y se encarga del tema de red. Se encarga de la comunicación interna del nodo con el cluster, pods, etc.

### 2.8 Container Runtime - ¡De nuevo Docker! <a name="#id2.8"> </a>

Cada nodo debe de tener instalado un mismo runtime, docker, crio, etc. que es el encargado de desplegar los contenedores o pods.
En las siguientes lecciones se verá lo que es un pod.

### 2.9 Conclusión de la Arquitectura <a name="#id2.9"> </a>

Kubernetes tiene complejidad pero hasta ahora los conceptos de arquitectura se mantienen básicos para mantener los conceptos sencillos.


## 3 Minikube <a name="#id3"> </a>


## 3.1 Instalación de Minikube Kubectl<a name="#id3.1"> </a>


Si ya tiene instalado kubectl
~~~
kubectl get po -A
~~~

En caso que no instalado, se puede descargar la versión apropiada 
~~~
minikube kubectl -- get po -A
~~~

Una vez instalado kubectl podemos comprobar ejecutando el comando: `kubectl version` 
Kubectl nos sirve para conectarnos con cualquier cluste de Kubernetes.
## 3.2 Instalación de Docker/VirtualBox <a name="#id3.2"> </a>

Minikube requiere un hypervisor o runtime como VirtualBox o Docker.

Para instalar Docker se pueden seguir los pasos de instalación en la [página de docker](https://docs.docker.com/engine/install/)

Nota: Es una buena idea agregar al usuario actual al grupo de docker. Para hacer esto se valida el usuario y se agrega al grupo:
~~~
whoami

sudo usermod -aG docker usuario
~~~

Para verificar la instalación de docker ejecute el comando `docker ps`

La otra opción es instalar con virtualbox, KVM etc.


## 2.3 Instalación de Minikube <a name="#id2.3"> </a>

Las instrucciones se encuentran en la [página de Kubernetes ](https://minikube.sigs.k8s.io/docs/start/) Y se selecciona la plataforma donde se va a instalar para seguir los pasos.

Para GNU/Linux son los siguientes comandos:

Descargue los binarios:  
~~~
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
~~~


Nota: Si no se tiene privilegios de root o sudo, se puede quitar el sudo y mover el binario al /usr/local/bin y desde ahí ejecutar todo con el usuario actual.

Nota: Si falla posiblemente hay que verificar los derechos de ejecución.


Para verificar que se haya instalado correctamente ejecute el comando: `minikube status` no debería de arrojar ningún estado en running porque no se tiene iniciado, pero debería arrojar una salida de type, host, kubelet, apiserver, y kubeconfig.


Para iniciar el cluster ejecute:
~~~
minikube start
~~~

Para iniciar el cluster y se usa virtualbox u otro runtime ejecute:  
`minikube start --driver=virtualbox`

Los driver preferidos son docker o kvm2, pero se puede cambiar el runtime por defecto con el comando: `minikube config set driver virtualbox` 

Cuando se usa docker no es necesario especificar driver (o se puede usar el driver "none") cuando se ejecuta en un host y no en una máquina virtual. Es decir si tiene docker y no un hyper visor.

Truco: Cuando se instala Minikube puede ser necesario ejecutarlo con sudo o root. Para una instalación de laboratorio de aprendizaje y facilitar el ambiente. Se puede modificar el bash.rc agregando dos líneas con los alias: `alias kubectl="sudo kubectl"` y en otra línea `alias minikube="sudo minikube"`. 
Esto se hace con un editor como `vi ~/.bash.rc`.  

Luego de agregar la linea guardar cambios y salir, ejecute el comando: `source ~/.bash.rc`.
Ahora se  puede ejeutar kubectl sin sudo y debería de funcionar. Por ejemplo el comando: `kubectl get pods`. Tal vez diga que no tiene ningún pod, porque no se ha desplegado nada, pero debería de funcionar sin root.

Nota: En la página de kubernetes sugiere ejecutar el siguiente comando: `alias kubectl="minikube kubectl --"`. Pero sólo dice que es para hacer la vida más fácil. Supongo sin asegurar que es similar al alias que el autor sugiere crear en el bash.rc.



## 3.4 Instalación de Minikube en Windows<a name="#id3.4"> </a>

Nota: Sección tomada literálmente como está en el curso.

Hola!


Vamos a ver en este artículo la instalación de Minikube en Windows. La instalación en este ejemplo se realizará con VirtualBox, pero si quieres hacerlo con Hyper-V en vez, no hay lío :)


Inicialmente, vamos a deshabilitar HyperV, si es que tienes Hyper-V.


* Vamos a Panel de Control->Programas->Activar o desactivar características de Windows. Luego, descativa Hyper-V.

* Verifica que la virtualización esté habilitada en la BIOS.

* Instala VirtualBox [Link aquí <--](https://www.virtualbox.org/wiki/Downloads) de la manera típica en Windows (Siguiente, siguiente)

* Decarga kubectl y colocalo disponible en tu path. [URL de descarga aqui](http://storage.googleapis.com/kubernetes-release/release/v1.4.0/bin/windows/amd64/kubectl.exe), [tutorial kubectl path](https://andrezgz.wordpress.com/2014/06/04/agregar-ruta-a-la-variable-path-en-windows/)

* Una vez tienes VirtualBox y kubectl, vamos con Minikube.

-> Url de descarga: https://github.com/kubernetes/minikube/releases


cambia el nombre a minikube.exe y colocalo en tu path, puedes colocarlo en el mismo folder donde colocaste la descarga de kubectl

Intentemos en PowerShell:

    kubectl.exe version
    minikube.exe version


* Hora de iniciar el cluster:

minikube.exe start --vm-driver="virtualbox"

Luego de esto, debería estar todo funcionando

    minikube.exe status
    kubectl.exe cluster-info


## 3.5 Comandos básicos de Minikube <a name="#id3.5"> </a>

**_minikube status_** Como su nombre indica el status de Minikube.

**_minikube start | stop _** Inicia o detiene el servicio de Minikube.

**_minikube delete_** Elimina todo el cluster.

## 4 Pods en Kubernetes VS contenedores Docker <a name="#id4"> </a>

### 4.1 ¿Cómo funciona un contenedor? <a name="#id4.1"> </a>

Los contenedores funcionan similar a una máquina virtual, tienen algunas similitudes pero aprovechan mas los recursos. Esto hace mas viable y barato que usar solamente maquinas virtuales.

Un contenedor se ejecuta de manera aislada en un namespace (Kubernetes también maneja un concepto de namespaces, pero aquí se refiere a cómo se ejecuta un contenedor ). Un  contenedor hereda varios namespaces. Como el contenedor se ejecuta aislado tiene que crear estos espacios de nombres para funcionar. La lista es la siguiente:

* IPC Inter Proccess Communication *.  
  Dentro de un contenedor, todos los procesos pueden comunicarse entre ellos. Pero entre dos contendores, no hay comunicación entre procesos. Este espacio de nombres es para la comunicación entre contendores de los PID, para que entre contenedores puedan "ver" los procesos del otro.

* Cgroup.  
  Controla los recursos de un contenedor, CPU, RAM, RED, Etc.
  
* Network*.  
  Cada contendor tiene una red lógica y pueden ver los procesos de red que estén en este namespace.
* Mount.  
  "Monta" los sistemas de archivos dentro de un contenedor. Cada contenedor puede tener sus propios directorios de montaje. (a menos que se comparta)
  
* PID.  
  Los process ID dentro de los contenedores. De la misma manera que los anteriores los PIDs solo son visibles dentro del contenedor.
* User.  
  Gestiona los usurios para cada contenedor.
  
* UTS. Unix Timesharing System*.  
  Habilita un hostname para cada pod.
  
>Nota Personal: El autor dice que el namespace UTS es para habilitar un hostname en los contenedores. El UTS tal vez participe en esa tarea, pero quizás no sea la tarea principal de ese espacio de nombres. _(Cómo cuando se crea un usuario en GNU/Linux se establece un tiempo Unix que entre otras "cosas" indica la vigencia de la cuenta de usuario)_.  
>
>En el whitepaper Unix Timesharing System de Dennis R. y Ken Thomsom, se refiere a cómo Unix en sus primeras versiones maximiza, optimiza y administra en general el tiempo de operaciones entre los usuarios y recursos del sistema, como lo son: el sistema de archivos, gerarquía, dispositivos, procesos y subprocesos, volumentes, Etc.
>
>Supongo que se toma el mismo concepto para poder administrar los recursos entre contenedores para crear el pod y no como tal para crear un "hostname" como literálmente lo dijo el autor.  
>
>Queda pendiente de corroborar lo que dice el autor sobre este espacio de nombres y lo que pude entender al respecto. Al momento de escribir esto no encontré una referencia mas a fondo del tema y lo dejo así por el momento.


###  4.2 ¿Cómo se crea un pod detrás de las escenas? <a name=#id4.2></a>

Un pod está formado por uno o más contenedores. Pero se ha dicho que trabajan de forma aislada. Para que un pod de varios contenedores funcione, necesitan compartir los recursos, como son la red, tienen la misma IP, pueden ver los procesos de otros contenedores en el mismo pod, Etc. De tal manera que se pueden comunicar.
Comparten los siguientes namespaces: Red, IPC y UTS. 

Inicialmente se crea un contenedor temporal con todos los elementos de los namespaces, luego se crean los otros posibles contenedores y heredan las propiedades del contenedor temporal. Cuando los contenedores necesarios se terminan de configurar el contenedor temporal se elimina.

En síntesis, cuando se crea un pod, este tendrá una sola IP para todos los contenedores, el mismo hostname y pueden ver sus Process ID.

Los nombres de espacio: cgroup, mount y user no son compartidos y siguen independientes en cada contenedor del pod.

## 5 Explorando Pods <a name="#id5"></a>

## 5.1 Crea tu primer pod <a name="#id5.1"></a>

* Primero verificar que Minikube esté corriendo: `minikube status`, si no lo está iniciarlo: `minikube start`.  
* Validar que kubectl esté funcionando con: `kubectl get pods`.  



Crear el pod

Para crear el pod se ejecuta `kubectl run generator=run-pod/v1 nombre_pod --image=nombre_imagen`

Nota Personal: Según el Autor, en versiones anteriores se ejecuta: `kubectl run NAME --image=image [--env="key=value"][--port=port]`, sin embargo la forma que se sugiere con _generator=run-pod/v1_ no ha funcionado, hay un error en la bandera: generator. 
Reinstalé varias veces y de varias formas (yast, zypper, binarios de kubernetes) en sus últimas versiones y el error persiste. Navegando en foros no ha sido clara la solución del error, y observando la referencia en la documentación de kubernetes.io, tal parece que en realidad la forma de creara un pod es al revés de como dice el autor, es decir la forma con: _generator=run-pod/v1_ es la forma vieja y no la nueva como señala el autor. Todavía se tiene confirmar esto. Pero es un hecho que la forma que sugiere el autor no me funciona con la versión mas reciente de kubectl ni minikube.

## 5.2 Describir un pod <a name="#id5.2"></a>

Para observar detalles de un pod, se usa **_describe_** como sigue: `kubectl describe pod nombre_pod`
El comando arrojará los detalles del pod y en la parte de eventos se describen los posibles errores.

## 5.3 Eliminar un pod <a name=#id5.3></a>

Para eliminar un pod se ejecuta el comando: `kubectl delete pod nombre_pod [nombre_pod2]...`

## 5.4 Obtener el manifiesto de un pod <a name=#id5.4></a>

Para obtener la información de un pod, se usa el comando: `kubectl get pod -o yaml`
Esto mostrará una salida en formato yaml de la configuración del pod. También pueden ser otros formatos como json indicando el formato de la salida deseado.

Como se ve en los detalles, cada pod tiene una IP que está sólo en el ambito local del pod y del cluster. Esto quiere decir que no es accesible desde fuera, por ejemplo desde otra computadora que se encuentre en la red.


## 5.5 ¿No puedes ver la IP de tu pod? <a name="#id5.5"></a>

Nota: Sección tomada tal cual del curso:

>Quizás tenga problemas con la siguiente lección y no puedas ver la IP del pod. Si es así, regresa aquí y observa la solución. De lo contrario, puedes ignorar este artículo.
>
>
>Solución:
>
>
>Si creaste tu cluster con algún driver como VirtualBox o HyperV, quizás no puedas ver la IP del pod debido a que se encuentran en una máquina distinta a tu localhost, por tanto no tienes acceso a estas IPs locales.
>
>Para solucionarlo, puedes ejecutar este comando:
>
>kubectl port-forward <pod-name> 7000:<pod-port>
>
>Luego, solo vas a http://localhost:7000 y deberías ver tu pod!


## 5.6 Obtener la IP de un pod <a name="#id5.6"></a>

Para ver la IP de un pod se usa el comando describe, y en la propiedad IP se encuentra indicada la IP interna del cluster.
   kubectl describe pod nombre_pod. Si el cluster no está en localhost, no vamos a poder acceder mediante esta IP al servicio. 

## 5.7 Ingreso a un contenedor dentro del pod <a name="#id5.7"></a>

Para ingresar al shell de un contenedor en un pod se usa la directiva **_exec_** con la opción **_-ti_** y al final se le indica **_-- sh_**.

Por ejemplo: `kubectl exec -ti nombre_pod -- sh` 


## 5.8 logs <a name="#id5.8"></a>

Se ejecuta el comando ]`kubectl logs nombre_pod [-f]` La opción -f es para darle seguimiento y la salida se queda en espera de nuevas entradas de log que se vallan producciendo hasta que se detenga.  


## 5.9 Acercamiento a manifiestos <a name="#id5.9"></a>

Los manifiestos definen los pods, sus recursos y configuración. Es la opción favorita para crear pods, debido a que dan facilidad para el manejo de uno o muchos pods. Esto facilita el trabajo, guarda un histórico porque puede ser versionado, no es necesario iniciar o eliminar pods manualmente y se pueden manejar muchos pods. 

Los manifiestos son archivos de configuracion preferentemente tienen el formato yaml . En la primer parte del archivo, se ponen los metadatos del pod, como la version, tipo, etiquetas, etc. En la siguientes partes , en el _spec_, se ponen las configuraciones de los contenedores como el nombre, imagen, puerto, etc.

¿Cómo se usa un manifiesto para crear un pod?

Se tiene que estar en la ruta donde se encuentra el manifiesto en una consola de comandos. Se usa el comando **_apply -f_** dónde la opción _-f_ sirve para especificar el nombre del manifiesto o archivo yaml.  

El siguiente es un archivo pod.yaml, y es un ejemplo de un manifiesto sencillo:

~~~                  ## Inicio de metadatos del pod
apiVersion: v1       ## Esta es la versión de la API
kind: Pod            ## Indica el tipo, en este caso es un tipo Pod
metadata:
  name: my-nginx     ## Nombre del Pod
spec:                ## Inicio de configuración de contenedor
  containers
  - name: nombre_cont ## Es el nombre del contenedor
   image: nginx     ## imagen que va a usar el contendor
---                  ## Separador  
apiVersion: v1       ## Esta es la versión de la API
kind: Pod            ## Indica el tipo, en este caso es un tipo Pod
metadata:
  name: my-nginx2     ## Nombre del Pod2
spec:                ## Inicio de configuración de contenedor
  containers
  - name: nombre_cont ## Es el nombre del contenedor
   image: nginx     ## imagen que va a usar el contendor
~~~

Los manifiestos pueden tener muchos mas elementos que no están en el ejemplo anterior.

Para usar el manifiesto se ejecuta el siguiente comando:  

`kubectl apply -f pod.yaml`

Se puede hacer un _kubectl get pod_ para observar los dos pod creados.

Para eliminar los pod usando el manifiesto, se usa el siguiente comando:  
`kubectl delete -f pod.yaml`

### 5.9.1 Crear dos contenedores en el mismo pod <a name="#id5.9"></a>

Es similar al archivo anterior llamado doscont.yaml, pero en vez de tener dos pod, en la sección spec se agregan los contenedores que se necesiten, en este ejeplo 2 contenedores. 

En este ejemplo también se va a agregar una línea mas que ejecuta un comando dentro del contenedor, que inicia un servidor web python en los puertos 8080 y 8081 respectivamente: 

~~~                  
apiVersion: v1       
kind: Pod            
metadata:
  name: dos_cont     
spec:                
  containers
  - name: cont1                 ## Es el nombre del contenedor 1
   image: python:3.6-alpine     ## imagen que va a usar el contendor
   command: ['sh', '.c', 'echo cont1 > index.html && python -m http.server 8080']
  - name: cont2                 ## Es el nombre del contenedor 2
   image: python:3.6-alpine     ## imagen que va a usar el contendor
   command: ['sh', '.c', 'echo cont2 > index.html && python -m http.server 8081']
~~~

Si los contenedores tuvieran el mismo puerto de escucha, entonces el pod se va a intentar iniciar pero fallará porque tienen un conflicto en el puerto. Sin embargo el pod sí intentará iniciarse.

Para rastrear este tipo de errores se tendrá que revisar con un `kubectl describe pod` o revisando los log con  `kubectl logs doscont` para este ejemplo.

### 5.9.2 Cómo ingresar al shell de un contendor en el pod <a name="#id5.10"></a>

Para ingresar al shell se ejecuta el comando _kubectl_ con  la cláusula _exec -ti_ seguida del nombre del pod, la opcion _-c  contenedor_ mas _-- sh_

    Por ejemplo para ingresar al shell de contenedor: cont1, definidoen el archivo: doscont.yaml, se ejecuta este comando:  
    kubectl exec -ti doscont -c cont1 -- sh
   
## 5.10 Labels y Pods <a name="#id5.10"></a>  

Los Labels o etiquetas son metadatos que se aplican a los pods y van situados debajo de la cláusula _"metadata"_ de un archivo yaml. Se pueden definir las etiquetas que se vallan a necesitar. Una etiqueta muy usada es **_app_** que se refiere a _aplicación_. Otra etiqueta puede ser _enviroment_ que se refiere al ambiente.  

    Ejemplo:  
~~~
    apiVersion: v1       
    kind: Pod            
    metadata:  
      name: pod_test2     
	  labels:
	    app: front  
		env: dev
    spec:                
      containers
      - name: cont1           ## Es el nombre del contenedor 1
       image: python:3.6-alpine     ## imagen que va a usar el contendor
       command: ['sh', '.c', 'echo cont1 > index.html && python -m http.server 8080']
    ---                       ## Separador  
    apiVersion: v1            ## Esta es la versión de la API
    kind: Pod                 ## Indica el tipo, en este caso es un tipo Pod
    metadata:
      name: pod_test3         ## Nombre del Pod2
	  labels:  
	   app: backend  
	   env: dev  
    spec:                     ## Inicio de configuración de contenedor
      containers
      - name: nombre_cont     ## Es el nombre del contenedor
       image: nginx           ## imagen que va a usar el contendor
~~~

Las etiquetas sirven para poder hacer filtros que cumplan con los criterios que se definan. También sirven para que los pod puedan ser administrados de mejor manera. Una buena práctica es por lo menos usar la etiqueta de aplicación: app.

### 5.10.1 Filtrar pod por labels <a name="#id5.10.1"></a>  

Para filtrar pod por alguna etiqueta se usa _get pods_ con  la cláusula _-l_ seguida de _etiqueta valor_:

`kubectl get pods -l etq=valor`

Por ejemplo realizar un filtro donde la etiqueta sea desarrollo:  
	`kubectl get pods -l env=dev`  

Esto listará los pods que contengan la etiqueta _env_ y que su valor sea _dev_.

## 5.11 "Problemas" con los pods <a name="#id5.11"></a>

Los pod por sí mismos no pueden replicarse, es decir que si una vez iniciado un pod, si se elimina manualmente, el pod por si solo no puede volver a iniciarse, o auto crearse.

No se pueden actualizarse los pod desde el pod mismo. Se tendría que hacer desde fuera, pero no hay una forma de auto actualizarse.


## 6 ReplicaSet <a name="#id6"></a>

Sirve para crear un número de réplicas que se le indiquen. Si una réplica muere, se va a levantar otra instancia. 

Para que esto pueda ocurrir el pod necesita tener la etiqueta: **_app_**. ReplicaSet va a revisar los pod en busca de esta etiqueta app con el valor que se le haya dado. Si no existe ningún pod con la etiqueta Replicaset lo va a crear.

__Nota__ __personal:__ No entendí si lo que dice el autor es que siempre un pod va a tener la etiqueta app, ya sea que se defina por nosotros o porque lo defina Replicaset de forma automática.

Otra revisión que hace ReplicaSet cuando crea el pod es que le va a registrar un _owner referemnce_ en la metadata del pod apuntando los pod a el mismo ReplicaSet. 

ReplicaSet es un objeto de alto nivel, para que pueda manejar los pod y mantiene un número definido de réplicas.


## Mi primer ReplicaSet <a name="#id6.1"></a>

Para crear un ReplicaSet se debe de crear un archivo yaml similar al de un pod, pero en el campo version el valor es: **_apps/v1_** y el campo **_kind_** el tipo NO es pod, tiene que ser **_ReplicaSet_**. Al igual que para los pod, también se le tiene que poner un nombre en el campo **_name_** al RéplicaSet.  
También se especifican  etiquetas **_app_** con un valor deseado.

En la sección **_spec_** se define lo que esperamos que haga este ReplicaSet, cuantas réplicas y el criterio de búsqueda por medio de las etiquetas **_selector_**. Por esta razón es la sección "Selector" del yaml.

En la sección **_template_** y las sucesivas líneas se definen los pod, tal cual como los ejemplos anteriores. Primero se define la metadata y en su propia sección spec se definen los contenedores. No es necesario poner la versión ni el campo kind, porque ya no se necesita.
Estos pods serán pertenecientes al ReplicaSet, así que la etiqueta app, debería de coincidir con los criterios de búsqueda definida en la sección Selector.  Pueden tener uno mas contenedores.

    Por ejeplo:

~~~                  
apiVersion: apps/v1       
kind: ReplicaSet          
metadata:
  name: rs-test     
  labels:
    app: rs-test
spec:                
  replicas: 5
  selector:
    matchLabels:
      app: pod-label
  template:
    metadata:
      labels:
        app: pod-label
    spec:
      containers:
      - name: cont1
       image: python:3.6-alpine
       command: ['sh', '.c', 'echo cont1 > index.html && python -m http.server 8080']
      - name: cont2
       image: python:3.6-alpine
       command: ['sh', '.c', 'echo cont2 > index.html && python -m http.server 8081']
~~~

Para ejecutar el replicaset descrito en el yaml con el comando: `kubectl apply -f rs.yaml`

Para eliminar el réplicaset se ejecuta: `kubectl delete -f rs.yaml`

## Cómo se relaciona un ReplicaSet con Pods <a name="#id6.2"></a>

Si se listan los replicaset se puede observar que existe un rs-test con 5 pods deseados, 5 actual, 5 Listos y el tiempo que lleva ejecutandose, conforme a lo que se definió en el manifiesto yaml.

Si se elimina un pod o por cualquier situación un pod falla, el réplicaset va a substituir el pod por uno nuevo, manteniendo el número de pods deseados.

Si se modifica el yaml con un nuevo número de réplicas, mayor o menor que lo que ya se tenía, se ejecuta de nuevo el comando `kubectl apply -f rs.yaml` y el réplicaset va a aumentar o eliminar el número de pods para cumplir con el manifiesto.

Un pod tiene definido en sus propiedades el réplicaset al que pertenece. Se puede observar obteniendo el yaml con el comando: `get pod nombre_pod -o yaml`

Dentro de la sección metadata hay una subsección que se llama "ownerReferences" que describe el nómbre, uid, versión y otras propiedades del réplicaset al que pertenece el pod. Estas propiedades deben de ser iguales a las propiedades del  y se puede observar con el comando: `get rs nombre_rs -o yaml`

## Adopción de Pods desde ReplicaSet  <a name="#id6.3"></a>

Si se crea un pod con alguna etiqueta y posteriormente se crea un réplicaset que en su selector concuerda con la etiqueta que tiene el pod ya creado, entonces el replica set lo va a administrar como suyo sin importar que no lo haya creado. Basta con que el criterio de búsqueda concuerde con las etiquetas de un pod.


Si un pod no tiene etiqueta se le puede agregar una con el comando: `kubectl label pods nombre_pod app=etiqueta` por ejemplo: `kubectl label pods podtest1 app=pod-label`.

Como el replicaset y el pod tienen las mismas etiquetas, el pod lo va tomar como suyo aunque se basen en distintas imagenes. 

Esto puede resultar en situaciones inesperadas, por lo que no se deberían de crear pods "planos" sino deberían de ser creados por replicaset o deployments.


## Problemas de los ReplicaSet <a name="#id6.4"></a>

Si se modifica un manifiesto en el número de réplicas de un replicaset y se aplica el cambio, se va a modificar el número de pods conforme al manifiesto, pero si se modifica alguna otra propiedad, se cambia la versión de la imagen etc. y se intenta aplicar el cambio el réplicaset no realizará ningún cambio, porque no detecta modificaciones en el número de réplicas. 
Se tendría que eliminar el replica set y aplicarlo de nuevo para que lo genere de nuevo con las nuevas especificaciones, pero no sirve para actualizar las especificaciones de los pod que están en ejecución. Es decir que el réplicaset si se va actualizar, pero los pods que ya se están ejecutando no.

Si se elimina un pod manualmente, el réplicaset generará un nuevo pod para sustituir el eliminado con la configuración nueva cumpliendo la especificación nueva y el número de réplicas.

RéplicaSet sirve para mantener un número de réplicas creado, pero si se quiere actualizar los pods replicaset no es lo mas adecuado.

### Nota: Replication Controller
___Replication___ ___Controller___ es similar al replicaset, pero está siendo substituido por este último. tienen el mismo propósito pero no es lo mismo. El siguiente ejemplo es tomado del curso: Certified Kubernetes Administrator.

A diferencia de los replicaset los replication controller no requieren selector ni matchlabels, pero su construcción es muy similar a la de un réplicaset

ReplicationController

rc-definition.yml

~~~
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end

spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-pod
    spec:
      containers:
      - name: nginx-container
        image: nginx
  replicas: 3
~~~

para crear el replica controller se ejecuta el comando: `kubectl create -f rc-definition.yml` y se pueden ver con el comando `kubectl get replicationcontroller`



## Deployments <a name="#id7"></a>

Los Deployments son dueños de los ReplicaSet como un ReplicaSet es dueño de un Pod. Es decir que al crear un deployment este va a crear un replicaset y este a su vez creará pods. Si se quiere actualizar la versión de la imagen 

Para crear un deployment se realiza un manifiesto y luego, se ejecuta el comando siguiente desde donde está el manifiesto:
`kubectl apply -f manifiesto.yaml`

El manifiesto tiene los elementos del nombre del deployment, información de numero de réplicas, Selector pods.

Por ejemplo:

~~~
apiVersion: apps/v1       
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
~~~



Para ver los deployments que están corriendo: `kubectl get deployment`

Se pueden ver los labels `kubectl get deployment --show-labels`

Si además se requiere ver el progreso y estado del deployment se puede usar el comando: `kubectl rollout status deployment nombre_deployment`

El manifiesto de deployment es muy similar al de un replicaset, pero el deployment si se puede actualizar mientras que el réplicaset no.



### Actualizar un deployment

Para actualizar un deployment se edita el manifiesto con los cambios requeridos. Luego se guarda y se ejecuta el comando:

~~~
kubectl apply -f nombre_deployment
~~~

Si se desea que se guarde el comándo en el histórico se agrega la opcción --record

~~~
kubectl apply -f nombre_deployment --record
~~~

Otra opción es poner una "annotations" (anotación) en el manifiesto en la parte del metadata del template:

~~~
kind: Deployment
metadata:
  annotations:
    kubernetes.io/change-cause:"Aquí va el comentario del cambio que se hizo, el puerto de escucha por ejemplo"
  name: nginx-deployment
  labels:
    app: nginx
...
~~~

En este caso ya no es necesario poner la opción --record.

### Para observar una revisión específica

`kubectl rollout history deployment nombre_deployment --revision=n`
donde n=numero de revisión 



### Rollback

`kubectl rollout undo deployment nombre_deployment --to-revision=n`
Donde n = número de revisión del deployment anterior

## Servicios <a name="#id8"></a>

### ¿Qué es un servicio  <a name="#id8.1"></a>

Los servicios se encargan de enlazar pods con el servicio a través de las etiquetas. Los servicios trabajan con un Endpoints. El endpoint es parecido a un directorio que va guardando listas de ips de los pods que se van generando o que son eliminados. De esta manera el servicio puede mantener comunicación con los pods consultando el endpoint.

Los pods conforme aparescan o desaparescan pueden estar cambiando de ip, pero el servicio siempre va a mantener una ip única.

### EndPoints  <a name="#id8.2"></a>

EndPoint es un directorio donde se registran los pods con sus correspondientes IPs. Esta lista es consultada por un servicio, para reenvíar las solicitudes a los pods que pueden dar respuesta a la solicitud de servicio.

El endpoint se mantiene actualizado conforme aparecen o desaparecen pods, para que el servicio pueda reenviar a los pods correspondientes.

Los endpoint pueden ir cambiando de ips. Los pods que se destruyen se eliminan de la lista mientras que los nuevos pods se agregan al endpoint.


### Crea tu primer servicio <a name="#id8.3"></a>

Crear un service.yaml con un deployment pero al final del archivo se debe de separar una sección con los caracteres: `---`

~~~
# definicion de deployment
apiVersion: apps/v1       
kind: Deployment
metadata:
  name: front-deployment
  labels:
    app: front         # label del deployment
#definición de replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: front       # label de replicaset
# El replicaset define los siguientes pods que se definen
  template:
    metadata:
      labels:
        app: front     # label del pod
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
---
## apartir de aquí es donde se define el servicio
apiVersion: v1
kind: Service
metadata:
  name: mi-servicio
  labels:
   app: front
spec:
  selector:
    app: front       # Debido a que el label del pod es front, se usa ese en este selector.
  ports:
    - protocol: TCP
      port: 80           # Este es el puerto del servicio
      targetPort: 80   # Este es el puerto que va a escuchar el pod, como nginx usa el 80, ponemos 80
~~~

Una vez que se tiene la definción en el yaml. Se ejecuta el comando:

`kubectl apply -f service.yaml`

Con estoy se crea un nuevo servicio

### Describe tu servicio y encuentra información útil <a name="#id8.4"></a>


Para obtener los servicios con la etiqueta front ejecute:  

`kubectl get svc -l app=front`

Nota: sino se le pone el filtro de la etiqueta, el comando va a regresar el servicio por default

En la salida del comando se muestran varias columnas. La columna CLUSTER-IP muestra la ip con la que nos podemos conectar al servicio con el puerto en la columna PORT(S). 

La columna NAME muesta el nombre del servcio y la columna AGE muestra cuanto tiempo tiene corriendo el Servicio.

Para "describir" el servicio se usa el Name:
`kubectl get svc NAME` Dónde Name corresponde al nombre del servicio.

Para describir un servicio por su etiqueta:

`kubectl describe svc -l app=front`

En este punto interesa la IP y el puerto. Los TargetPort y la lista EndPoints corresponden a los pods.

### Pods y EndPoints <a name="#id8.5"></a>

Para listar los endpoint:
`kubectl get endpoints`

comparando con los pods del servicio:

kubectl ged pods -l app=front -o wide`

Se observa que las ips de los pods concuerdan con los endpoints.

### Servicios y DNS <a name="#id8.6"></a>

Los servicios se pueden llamar a traves de su ip:puerto o también se pueden llamar a través del nombre del servicio. Por ejemplo: mi-servicio:80. Este DNS es heredado del nombre en la etiqueta name del manifiesto del servicio.

Por ejemplo:

~~~
...
apiVersion: v1
kind: Service
metadata:
  name: mi-servicio
  labels:
   app: front
...
...
~~~

Se puede llamar el servicio con el comando: `curl mi-servicio:80`. El puerto 80 se puso con cierta obviedad para indicar que se puede llamar con el puerto que tenga publicado el servicio.




### Servicio de tipo ClusterIP <a name="#id8.7"></a>

Son servicios que kubernetes le asigna una IP virtual y que va a ser permanente. Esta ip es interna del cluster y se va a poder acceder desde el cluster con esa ip. Esto quiere decir que es visible solo en la máquina donde está el cluster y dentro del cluster, pero desde otro punto de la red no, porque es "internal IP".  

De forma predeterminada este servicio no es accesible desde internet (o externamente). Para acceder necesitamos habilitar el acceso a través del proxy de kubernetes con el siguiente comando:

`kubectl proxy --port = 80`

___Nota___ ___personal:___ La documentación no dice cuál ip se usa luego de aplicar el comando anterior proxy. Supongo que debe ser la ip del servicio, pero falta realizar una prueba con la ip definida en el servicio para ver cómo funciona.  



Para definir el tipo de cluster podemos poner la etiqueta "type:"  en el área donde está el spec del servicio:

~~~
apiVersion: v1
kind: Service
metadata:
  name: mi-servicio
  labels:
   app: front
spec:
  type:ClusterIP
  selector:
    app: front  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
~~~



El ClusterIP es el tipo por defecto. Otros tipos de servicio son:

* Nodeport
* LoadBalancer
* ExternalName
* Ingress (No es un servicio propiamente pero tiene una gran relación, es mas bien un enrutador que permite la entrada al cluster)





###  Servicio de tipo NodePort <a name="#id8.8"></a>  

Es otro tipo de servicio que permite exponer el servicio fuera del cluster. Está al nivel del nodo y sirve para exponeer un servicio fuera.

Internamente para funcionar, el tipo NodePort crea un tipo ClusterIP, pero gestiona él mismo el acceso al interior.

~~~
# definicion de deployment
apiVersion: apps/v1       
kind: Deployment
metadata:
  name: backend-deployment
  labels:
    app: backend         # label del deployment
#definición de replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend       # label de replicaset
# El replicaset define los siguientes pods que se definen
  template:
    metadata:
      labels:
        app: backend     # label del pod
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
---
## apartir de aquí es donde se define el servicio
apiVersion: v1
kind: Service1
metadata:
  name: mi-servicio
  labels:
   app: backend
spec:
  type:NodePort
  selector:
    app: backend       # Debido a que el label del pod es backend, se usa ese en este selector.
  ports:
    - protocol: TCP
      port: 80           # Este es el puerto del servicio
      targetPort: 80   # Este es el puerto que va a escuchar el pod, como nginx usa el 80, ponemos 80
~~~

NodePort expone un rango de puertos por defecto, que van desde el 30000 al 32767. Si no se define el puerto en el manifiesto, se va a publicar el servicio por un puerto automático que esté dentro del rango.

Para ver cual es el puerto que kubernetes asignó automáticamente a este servicio se ejecuta el comando `kubectl getsvc -l  app=backend` en la columna PORT se puede observar 'puerto interno': 'puerto automático'. Luego se puede usar la ip local del servidor donde está el cluster con el puerto automático desde otro punto de la red local. 
___Nota:___ La ip indicada en la salida del comando `kubectl getsvc -l  app=backend` coluna CLUSTER-IP, es la ip interna por lo que no será accesible desde otro equipo en la red local.


###  Servicio de tipo LoadBalancer <a name="#id8.9"></a>  

Este servico es de tipo de balanceador de cargas, y se usan en balanceadores de cargas en la nube. Por lo tanto por el momento no se va a ejeplificar como los servicios anteriores.

El servicio de tipo balanceador de cargas, crea a su vez un servicio de tipo nodeport, que a su vez crea un servicio ClusterIP para funcionar.

###  Golang, JavaScript y Kubernetes. Aprende a construir aplicaciones reales <a name="#id9"></a>  

_Atemcoion:_ Laboratorio pendiente de escribir en las notas

Hacer que Kubernetes busque imagen localmente 

Se debe de moficar la política "image push policy" del valor "Always" a "IfNotPresent"

Para observar cómo está configurada la política en un Deployment se ejecuta: `kubec get deployments.apss "nombre del deployment" -o yaml | grep -i Pull` 
la salida encontrará la línea de configuracion que deberia ser similar a esta:
`imagePullPolicy: Always`

Sin filtrar con el grep, o buscando con grep con algunas líneas antes y después del contexto (P.E. grep -C 12 Pull) Se puede observar que la política se encuentra en la sección de Containers. Así que se puede editar el manifiesto y agregar la instrucción "imagePullPolicy: IfNotPresent" en nuestro manifiesto y luego aplicar el deployment. 



## Apendice

Para ver las versiones de API disponibles:

`kubectl api-versions`

Para ver la lista de objetos en kubernetes donde se miestra nombres, nombre corto, su api-group, el espacio de nombres y el tipo
`kubectl api-resources`

Para ver en formato amplio la salida de get pods que tengan la etiqueta front:
`kubectl get pods -l app=front -o wide

Para crear un replicaset:
`kubectl create -f replicaset-definicion.yml`

Para ver los replicaset:
`kubectl get replicaset`

Para eliminar un replicaset (y sus pods)
`kubectl delete replicaset replicaset-definicion.yml`

Para aplicar un remplazo de un manifiesto modificado de un replicaset:
`kubectl replace -f manifiesto.yml`

Para escalar el número de réplicas usando _scale_ :
`kubectl scale --replicas=n -f manifiesto.yml` 
dónde n= número de réplicas

Para "explicar" un comando. Ejemplo Replicaset:
`kubectl explain replicaset`
Mostrará algo similar al "man" de unix

Tips
Create an NGINX Pod

`kubectl run nginx --image=nginx`

Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

`kubectl run nginx --image=nginx --dry-run=client -o yaml`

Create a deployment

`kubectl create deployment --image=nginx nginx`

Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) and save it to a file.

`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`

Make necessary changes to the file (for example, adding more replicas) and then create the deployment.

`kubectl create -f nginx-deployment.yaml`


OR

In k8s version 1.19+, we can specify the --replicas option to create a deployment with 4 replicas.

`kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml`



## Glosario <a name="#glos"></a>

* kubectl get pods[-o]<a name="#getpods"></a>
Obtiene una lista de los pods o si se indica, de un pod específico. Se puede usar con formato de salida con la opción -o(output)  
Uso: kubectl get  [(-o|--output=)json|yaml|name|go-template|go-template-file|template|templatefile|jsonpath|jsonpath-as-json|jsonpath-file|custom-columns|custom-columns-file|wide] (TYPE[.VERSION][.GROUP] [NAME | -l label] | TYPE[.VERSION][.GROUP]/NAME ...) [flags]  

   Referencia: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
   
* kubectl run <a name="#run"></a>  
   Crea y ejecuta una imagen en un pod.
   kubectl run NAME --image=image [--env="key=value"] [--port=port] [--dry-run=server|client] [--overrides=inline-json] [--command] -- [COMMAND] [args...]  
   Referencia: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run  
   
* api-resources <a name="#api-resources"></a>
   imprime los recursos del API  
   referencia: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#api-resources
   
   
