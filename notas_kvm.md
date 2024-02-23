# Notas KVM

## Instalando libvirt en Debian

Este es un cliente que soporta KVM, Qemu, y otros virtualizadores. Es un cliente gráfico. y se puede ejecutar  redireccionando las X vía SSH, con opción -X, o -Y. La diferencia entte estas es que la Y se considera conexión confiable, mientas que la X no.

Para instalar: 
~~~
sudo apt-get install qemu-system libvirt-clients libvirt-daemon-system virtinst Virt-manager bridge-utils
sudo apt install     qemu-kvm virt-manager
# Obtener ayuda de virsh 
~~~

Si es necesario ejecutar como root el cliente virt-manager vía redirección de X, es posible que aparesaca un errror similar a este:

> $ sudo virt-manager
> soporte@debian:~$ X11 connection rejected because of wrong authentication.
> Unable to init server: No se pudo conectar: Conexión rehusada
> X11 connection rejected because of wrong authentication.
> Unable to init server: No se pudo conectar: Conexión rehusada
> X11 connection rejected because of wrong authentication.
> Unable to init server: No se pudo conectar: Conexión rehusada
> 
> (virt-manager:1158): Gtk-WARNING **: 12:33:26.186: cannot open display: localhost:11.0

Se puede usar la cookie del usuario que establece la conexión. Hay varios procedimientos y trucos para hacer eso. Aquí hay uno:
~~~
1.- ssh user@host
2.- $ sudo su
3.- # xauth merge /home/user/.Xauthority
~~~

Luego se puede ejecutar: `sudo Virt-manager`
https://qastack.mx/superuser/409186/environment-variables-in-bash_profile-or-bashrc


## Modificar variable SHUTDOWN_TIMEOUT

Modifique el archivo: /usr/lib/libvirt/libvirt-guests.sh
y modifique la variable: SHUTDOWN_TIMEOUT=300 y cambie su valor a 60

## Uso del comando virsh con usuario normal

Al ejecutar virsh ( por lo menos en Debian) el binario de virsh utiliza el uri 'qemu:///session'  por lo que cualquier comando virsh  no se comporta igual que cuando se ejecuta sudo o es root.

Por ejemplo: 

Listar los dominios en el sistema:

~~~
soporte@debian:~$ virsh list --all
 Id   Nombre   Estado
-----------------------

soporte@debian:~$
~~~
No se observa ningún dominio. Pero si se ejecuta con sudo:

~~~
soporte@debian:~$ sudo virsh list --all
[sudo] password for soporte:
 Id   Nombre           Estado
--------------------------------
 -    win_2019_dc_ts   apagado

soporte@debian:~$
~~~

La solución es conectar con una URI qemu:///system
~~~
soporte@debian:~$ virsh --connect qemu:///system list --all
 Id   Nombre           Estado
--------------------------------
 -    win_2019_dc_ts   apagado

soporte@debian:~$
~~~

Una alternativa es realizar lo siguiente:

>Descomentar esta línea en el archivo /etc/libvirt/libvirt.conf
> 
> `uri_default = "qemu:///system"
> 
> fue suficiente para mí en fedora 29 .
> 
> Editar: como dice aquí https://libvirt.org/uri.html para los usuarios que no son root ese archivo también tiene que estar en $XDG_CONFIG_HOME/libvirt/libvirt.conf
> 
> que en mi caso es:
> 
>  ~/.config/libvirt/libvirt.conf
> 
> así que copié el archivo allí (en mi instalación fresca) y ahora virsh net-list funciona como un usuario no root y sin necesidad de espicificar --connect
> 

Fuente: https://www.enmimaquinafunciona.com/pregunta/219608/como-puedo-listar-las-redes-virsh-sin-sudo
https://libvirt.org/uri.html#qemu-qemu-and-kvm-uris

En mi caso particular probé con buenos resultados agregar en el archivo /home/soporte/.profile la variable: "LIBVIRT_DEFAULT_URI" quedando de la siguiente manera:

`export LIBVIRT_DEFAULT_URI="qemu:///system"`

Con esto al iniciar la sessión no se tiene que estr usando sudo, o bien escribir siempre la URI.


## Comando virsh

`$ virsh help`

El comando anterior listará comandos que se agrupan en secciones

Cada sección contiene los comandos relacionados para realizar un conjunto particular de tareas. Puede ver la sección de ayuda de un grupo, por ejemplo, Networking, como se muestra a continuación:

`$ virsh help Networking`

También puede mostrar la sección de ayuda de un comando específico. Como:

~~~
$ virsh help net-name

NAME
net-name: convierte un UUID de red en un nombre de red
...
~~~

# Crear Máquina virtual con virsh

Para crear la nueva máquina virtual ejecute el comando:

~~~
sudo virt-install --name=squid-vm \
--os-type=Linux \
--os-variant=debian9 \
--vcpu=2 \
--ram=2048 \
--disk path=/mnt/hd2/MV/squid.qcow2,size=15 \
--graphics spice \
--location=/mnt/hd2/isos/debian-11.6.0-amd64-netinst.iso \
--network bridge:brpub 
~~~

   * El atributo –name denota el nombre de la máquina virtual. Siéntase libre de darle un nombre arbitrario.
   * La directiva –os-type especifica el tipo de sistema operativo, en este caso Linux.
   * La opción –os-variant especifica las versiones del sistema operativo.

NOTA: KVM proporciona opciones de variantes de sistema operativo predefinidas y usted simplemente no puede crear las suyas propias. Para verificar las diversas variantes que se admiten, ejecute el comando osinfo-query os. Aquí se enumeran todos los sistemas operativos posibles y las variantes admitidas. Además, tenga en cuenta que las variantes pueden no coincidir con su última versión de Linux. En este caso, estoy usando debian9 en lugar de debian11 ya que KVM no proporciona este último como una de las opciones variantes.

   * Continuando, el parámetro –vcpu especifica la cantidad de núcleos de CPU que se asignarán a la máquina virtual.
   * La opción –ram especifica la cantidad de RAM en Megabytes que se asignará.
   * La opción –disk path define la ruta de la imagen de la máquina virtual. La directiva –disk es el espacio en disco de la VM en Gigabytes.
   * La opción –graphics especifica la herramienta gráfica para la instalación interactiva, en este ejemplo, estamos usando especias.
   * La opción –ubicación apunta a la ubicación de la imagen ISO
   * Por último, la directiva –network bridge especifica la interfaz que utilizará la máquina virtual.
   
   
## Para enumerar todas las máquinas virtuales, incluidas las que están apagadas, agregue la opción –all.

virsh list --all

## Renombrar una máquina virtual (dominio)  

El comando virsh domrename se utiliza para cambiar el nombre de un dominio. Este comando cambia el nombre de dominio actual por el nuevo nombre sin realizar ningún cambio en los archivos de configuración.

Tenga en cuenta que el dominio debe estar inactivo y sin instantáneas o puntos de control.

`$ virsh domrename nombre_actual nuevo_nombre`

## Guardar y Restaurar máquinas virtuales

Para guardar el estado actual de una máquina virtual en ejecución, puedes ejecutar save, así:

~~~
$ virsh save AlmaLinux almalinux-save
Domain AlmaLinux saved to almalinux-save
~~~

Este comando detiene al guest y guarda los datos en un archivo llamado almalinux-save. Para restaurar el estado guardado previamente de una máquina virtual, simplemente especifique el nombre del archivo como se muestra a continuación:

~~~
$ virsh restore almalinux-save
Domain restored from almalinux-save
~~~

## Reiniciar y Suspender/Pausar máquinas virtuales

Para reiniciar una máquina virtual en ejecución, ejecute:
~~~
$ virsh reboot AlmaLinux
...
~~~

Sí quieres suspender una máquina virtual, basta con:

~~~
$ virsh suspend AlmaLinux
...
~~~

Este estado es considerado como pausado, también esta el comando resume, que nos ayuda a reactivar, a reanudar la VM, saliendo de la pausa:

~~~
$ virsh resume AlmaLinux
...
~~~

# Detener y Apagar máquinas virtuales activas

## Para apagar una máquina virtual

`sudo virsh shutdown vm_name`


## Para apagar nuestra máquina virtual


`sudo virsh shutdown debian-vm`


## Para iniciar o encender la máquina virtual

`sudo virsh start debian-vm`


## Para reiniciar la máquina

`sudo virsh reboot debian-vm`


## Para detener por la fuerza la máquina virtual

`sudo virsh destroy debian-vm`

## Para detener con gracia en vez de forzar una máquina virtual

`sudo virsh destroy debian-vm --graceful`

## Para eliminar o eliminar una máquina virtual junto con su archivo de disco

a) Primero apague la máquina virtual

b) Elimine la máquina virtual junto con su archivo de almacenamiento asociado, ejecute

`$ sudo virsh undefine –domain –remove-all-storage`

Ejemplo:

`$ sudo virsh undefine --domain debian-vm --remove-all-storage`

## Para iniciar automáticamente un dominio al arranque del host

virsh autostart dominio

Ejemplo:
~~~
virsh autostart maquina_virtual
~~~

### Para deshabilitar el inicio automático de un dominio al arranque del host
virsh autostart --disable dominio

Ejemplo:
~~~
virsh autostart --disable maquina_virtual
~~~



## Obtener una lista de los sistemas operativos soportados

Se tiene que tener instalado libosinfo-bin. Si no se tiene instalado: `sudo apt install libosinfo-bin`

Luego se pueden listar los sistemas operativos soportados: `osinfo-query os`

## Crear y Recuperar maquinas virtuales con volcado XML

Puede crear una nueva máquina virtual invitada utilizando el XML existente de huespeds creados anteriormente. Primero, cree un volcado XML como se muestra arriba y luego cree una nueva VM usando el archivo XML como se muestra a continuación:

~~~
$ virsh create AlmaLinux.xml
...
~~~

Este comando creará una nueva máquina virtual y la iniciará de inmediato. Puedes verificarlo usando el comando virsh list, arriba mencionado. Para mostrar el archivo de configuración XML de una máquina virtual en la salida estándar, ejecute:

~~~
$ virsh dumpxml Almalinux
...
~~~

Mostrando los detalles completos de la configuración (software y hardware) de la máquina virtual. Tambien puedes exportar el volcado XML a un archivo en lugar de simplemente mostrarlo en la salida estándar

`$ virsh dumpxml AlmaLinux > AlmaLinux.xml`

El archivo xml, se guardará en el directorio de trabajo actual. Si deseas realizar cambios en las guest, simplemente puedes editar su archivo de configuración y realizar los cambios que desee; tambien se pueden editar mientras corren o mientras están fuera de línea.

`$ virsh edit AlmaLinux`

Este comando abrirá el archivo en su editor predeterminado que configuró con la variable $EDITOR.
Habilitar el acceso a la consola para máquinas virtuales

Después de crear máquinas invitadas KVM, puedes acceder a ellas a través de SSH, cliente VNC, Virt-viewer, Virt-manager, etc. Sin embargo, no puedes acceder a ellas usando el comando virsh console (se utiliza para conectar la consola serie virtual para el huesped). Para acceder a los huespeds de KVM mediante esta, debes habilitar el acceso a la consola en serie en la máquina invitada.

Debes agregar una consola serie en su máquina invitada para acceder a su consola virsh desde el sistema host. Para hacerlo, inicia sesión en su máquina invitada a través de SSH o Virt-manager, ejecutando los siguientes comandos para habilitar e iniciar una consola en serie:

~~~
# systemctl enable serial-getty@ttyS0.service 
# systemctl start serial-getty@ttyS0.service 
~~~

**Atención:** Tener en cuenta que los comandos anteriores deben ejecutarse en el sistema huesped KVM (máquina virtual), no en el host KVM. Puedes verificar mirando en el archivo XML de configuración de la máquina virtual. Mostrando algo similar a esto:

~~~
<serial type='pty'>
    <target type='isa-serial' port='0'>
         <model name='isa-serial'/>       
    </target>     
</serial>
<console type='pty'>       
    <target type='serial' port='0'/>     
</console> 
~~~


Ahora inicie la consola virsh del sistema huesped desde el host usando el comando:

~~~
$ virsh console AlmaLinux 
Connected to domain AlmaLinux 
Escape character is ^] 
~~~

Presiona ENTER nuevamente y escriba su nombre de usuario y contraseña para conectarse a la máquina invitada Ahora está dentro de la consola de la máquina invitada. Empiece a usarlo. Puede volver a la consola del host en cualquier momento presionando las teclas CTRL +].


## Obtener IPs de una máquina Virtual

`sudo virsh domifaddr nombre_mv`


## Conectar a máquina virtual 

`virsh -c qemu+ssh://usuario@servidor/system`

## Redes KVM

### Configuración de redes de huesped

#### Configuar IP fija en la máquina virtual

#### Conexión tipo nat 

Se define en el archivo xml mediante las etiquetas. La plantilla siguiente es un ejemplo de una implementación:

~~~
...
<devices>
  <interface type='network'>
    <source network='default'/>
  </interface>
  ...
  <interface type='network'>
    <source network='default' portgroup='engineering'/>
    <target dev='vnet7'/>
    <mac address="00:11:22:33:44:55"/>
    <virtualport>
      <parameters instanceid='09b11c53-8b5c-4eeb-8f00-d84eaa0aaa4f'/>
    </virtualport>
  </interface>
</devices>
...
~~~

#### Conexión a puente público  

Al momento de escribir estas notas no he podido establecer la configuración mediante el archivo de configuración xml con una conexión de puente público (bridge) y tal vez no se pueda lograr. 
A pesar de que al volcar la configuración de un dominio que esté funcionando (en puente NAT o default) generado con el comando dumpxml, en el volcado se observa la etiqueta _ip_ _address_, pero esta etiqueta no puede ser indicada en el xml para fijar la propiedad IP/Máscara si el dominio se va a conectar a un puente público. Esto se debe a que quemu no puede controlar ni gestionar el puente de red.

La siguiente nota tomada de https://serverfault.com/questions/1076615/loading-a-kvm-guest-with-static-ip-using-xml-file lo explica:

>No hay forma de configurar 'ip estática' en invitado. Lo más parecido es tener DHCP en el invitado, y luego virsh/qemu puede proporcionar 
>la IP configurada al invitado mediante DHCP; sin embargo, dado que está utilizando una red en puente, virsh/qemu ya no tiene el control de
>la interfaz y necesita configurar esto en el servidor DHCP de su red; puede usar la dirección MAC de los invitados para hacerlo.

Por lo anterior para poder configurar un dominio con ip fija, no se realiza del lado de la configuración del dominio, si no que se tiene que realizar la configuración en el lado del huesped como se haría con una máquina real. En sistemas tipo Debian se configura en el archivo /etc/network/interfaces, en los sistemas tipo Redhat en el archivo correspondiente a la interaz de red en el directorio /etc/sysconfig/network-scripts/ifcfg-eth0.

Un ejemplo de la configuración de una interfaz de red sería similar a la siguiente:

~~~
...

## ---- INTERFAZ RED LOCAL e internet ----
auto eth0
iface eth0 inet static
        address 192.168.101.251
        netmask 255.255.255.0
        gateway 192.168.101.254
...
~~~




### Distintos tipos de configuración de red


Caso de uso:

* Desea una forma sencilla para que su máquina virtual acceda al host, a Internet a los recursos disponibles en su red local.
* No necesita acceder a su huesped desde la red o desde otro huesped.
* Estás listo para recibir un gran golpe de rendimiento.
* **Advertencia:** la red de usuarios no es compatible con una serie de funciones de red como ICMP. Ciertas aplicaciones (como ping) pueden no funcionar correctamente.

Requisitos previos:

* Necesita kvm en funcionamiento
* Si no desea ejecutar como root, entonces el usuario debe tener acceso rw a /dev/kvm
* Para que el huesped pueda acceder a Internet o a una red local, el sistema host también debe poder acceder a estos recursos.

Solución:

Simplemente ejecute su huesped sin especificar los parámetros de red, que de forma predeterminada crearán una red a nivel de usuario (también conocida como slirp):
~~~
qemu-system-x86_64 -hda /ruta/a/hda.img
~~~

Notas:

La dirección IP se puede asignar automáticamente al huésped gracias al servicio DHCP integrado en QEMU
Si ejecuta varios huespeds en el host, no necesita especificar una dirección MAC diferente para cada huesped
El valor predeterminado es equivalente a esta configuración explícita:
~~~
qemu-system-x86_64 -hda /ruta/a/hda.img -netdev usuario,id=usuario.0 -device e1000,netdev=usuario.0
~~~

* El identificador user.0 anterior es solo para conectar las dos mitades en una. Puede utilizar cualquier identificador que desee, como 'n' o 'net0'.
* Utilice rtl8139 en lugar de e1000 para obtener una interfaz de red basada en rtl8139.
* Todavía puede acceder a un puerto específico en el huesped usando la opción 'hostfwd'. Esto significa, p. si desea transportar un archivo con scp del host al huesped, inicie el huesped con '-device e1000,netdev=user.0 -netdev user,id=user.0,hostfwd=tcp::5555-:22'. Ahora está reenviando el puerto de host 5555 al puerto de huesped 22. Después de iniciar el huesped, puede transportar un archivo con, p. 'scp -P 5555 file.txt root@localhost:/tmp' del host al huesped. O también puede usar la otra dirección del host para conectarse.

### Puente virtual privado

Caso de uso:

Desea configurar una red privada entre 2 o más máquinas virtuales. Esta red no se verá desde las otras máquinas virtuales ni desde la red real.

requisitos previos:

* Necesita kvm en funcionamiento
* Si no desea ejecutar como root, entonces el usuario debe tener acceso rw a /dev/kvm
* Los siguientes comandos deben instalarse en el sistema host y ejecutarse como root:
~~~
ip
brctl (obsoleto). Use el enlace IP en su lugar
tunctl (obsoleto). Use ip tuntap e ip link en su lugar
~~~

Solución:

Necesitas crear un puente, por ejemplo:
~~~
# ip link add br0 type bridge ; ifconfig br0 up
# brctl addbr br0 (obsoleto)
~~~

Necesita un script qemu-ifup que contenga lo siguiente (ejecutar como root):

~~~
#!/bin/sh
set -x

switch=br0

if [ -n "$1" ];then
        # tunctl -u `whoami` -t $1 (use ip tuntap instead!)
        ip tuntap add $1 mode tap user `whoami`
        ip link set $1 up
        sleep 0.5s
        # brctl addif $switch $1 (use ip link instead!)
        ip link set $1 master $switch
        exit 0
else
        echo "Error: no interface specified"
        exit 1
fi
~~~

 
Genere una dirección MAC, ya sea manualmente o usando:
~~~
#!/bin/bash
# generate a random mac address for the qemu nic
printf 'DE:AD:BE:EF:%02X:%02X\n' $((RANDOM%256)) $((RANDOM%256))
~~~

Ejecute cada huesped con lo siguiente, reemplazando $macaddress con el valor del paso anterior

~~~
qemu-system-x86_64 -hda /ruta/a/hda.img -device e1000,netdev=net0,mac=$macaddress -netdev tap,id=net0
~~~

Notas:

Si no desea ejecutar qemu-ifup como root, considere usar sudo.
Puede crear un qemu-ifup para todo el sistema en /etc/qemu-ifup o usar otro. En este último caso, ejecute

~~~
qemu-system-x86_64 -hda /ruta/a/hda.img -device e1000,netdev=net0,mac=$macaddress -netdev tap,id=net0,script=/ruta/a/qemu-ifup
~~~

Cada huesped en la red virtual privada debe tener una dirección MAC diferente


## Puente público

ADVERTENCIA: El método que se muestra aquí no funcionará con todos los controladores inalámbricos, ya que es posible que no admitan puentes.

Caso de uso:

Quiere asignar direcciones IP a sus máquinas virtuales y hacerlas accesibles desde su red local
También desea obtener rendimiento de su máquina virtual


requisitos previos:

* Necesita kvm en funcionamiento
* Si no desea ejecutar kvm como root, el usuario debe tener acceso rw a /dev/kvm
* Los siguientes comandos deben instalarse en el sistema host y ejecutarse como root:
* sudo apt install bridge-utils

~~~
ip
brctl (en desuso, use ip link en su lugar)
tunctl (en desuso, use ip tuntap en su lugar)
~~~

* Su sistema host debe poder acceder a Internet o a la red local


**Solución 1: uso de secuencias de comandos específicas de distribución**

---
  |   RedHat                                        |  Debian                                           |SuSE                                    |
  |-------------------------------------------------|---------------------------------------------------|----------------------------------------|
  |  Edit /etc/sysconfig/network-scripts/ifcfg-eth0 |  /etc/network/interfaces                          | Start YaST                             |
  |     comment out BOOTPROTO                       |  # Replace old eth0 config with br0               | Ir a Configuración de red              |
  |     Add BRIDGE=br0                              |  auto *~~eth0~~* br0                            | Add new device -> Bridge               |
  | Create /etc/sysconfig/network-scripts/ifcfg-br0 |                                                   | Marque su dispositivo de red existente |
  |     The content should be:                      |  # Use old eth0 config for br0, plus bridge stuff | Hecho                                  |
  |  DEVICE=br0                                     |  iface br0 inet dhcp                              |                                        |
  |  BOOTPROTO=dhcp                                 |    bridge_ports    eth0                           |                                        |
  |  ONBOOT=yes                                     |    bridge_stp      off                            |                                        |
  |  TYPE=Bridge                                    |    bridge_maxwait  0                              |                                        |
  |                                                 |    bridge_fd       0                              |                                        |

~~~
/etc/init.d/networking restart
~~~
El puente br0 debe obtener la dirección IP (ya sea estática/dhcp) mientras que el eth0 físico se queda sin una dirección IP.


### VLANs

Tenga en cuenta que el controlador de interfaz de red virtual rtl8139 no admite VLAN. Si desea usar VLAN con su máquina virtual, debe usar otra interfaz de red virtual como *virtio*.

Cuando usa VLAN en una configuración como esta y no llega tráfico a sus huespeds, es posible que desee hacer lo siguiente:

~~~
# cd /proc/sys/net/bridge
# ls
bridge-nf-call-arptables  bridge-nf-call-iptables
bridge-nf-call-ip6tables  bridge-nf-filter-vlan-tagged
# for f in bridge-nf-*; do echo 0 > $f; done
~~~



**Solución 2: Configuración manual**

Necesitas crear un puente, por ejemplo:
~~~
# ip link add br0 type bridge
# brctl addbr br0 (en desuso, ¡use ip link en su lugar!)
~~~

Agregue una de sus interfaces físicas al puente, por ejemplo, para eth0:

~~~
# ip link set eth0 master br0
# brctl addif br0 eth0 (en desuso, ¡use ip link en su lugar!)
~~~

Necesita un script qemu-ifup que contenga lo siguiente (ejecutar como root):

~~~
#!/bin/sh
set -x

switch=br0

if [ -n "$1" ];then
        #tunctl -u `whoami` -t $1
        ip tuntap add $1 mode tap user `whoami`
        ip link set $1 up
        sleep 0.5s
        #brctl addif $switch $1
        ip link set $1 master $switch
        exit 0
else
        echo "Error: no interface specified"
        exit 1
fi
~~~

Genere una dirección MAC, ya sea manualmente o usando:
~~~
#!/bin/sh
# generate a random mac address for the qemu nic
printf 'DE:AD:BE:EF:%02X:%02X\n' $((RANDOM%256)) $((RANDOM%256))
~~~

Ejecute cada huesped con lo siguiente, reemplazando $macaddress con el valor del paso anterior
~~~
qemu-system-x86_64 -hda /path/to/hda.img -device e1000,netdev=net0,mac=$macaddress -netdev tap,id=net0
~~~

*Notas:*

* Si no desea ejecutar qemu-ifup como root, considere usar sudo
* Cada huesped en la red debe tener una dirección MAC diferente
* Puede crear un qemu-ifup para todo el sistema en /etc/qemu-ifup o usar otro. En este último caso, ejecute:
~~~
qemu-system-x86_64 -hda /path/to/hda.img -device e1000,netdev=net0,mac=$macaddress -netdev tap,id=net0,script=/path/to/qemu-ifup
~~~


### Enrutamiento con iptables

Con este método, puede conectar su máquina virtual invitada a un dispositivo tap en su host. Luego, puede configurar las reglas de iptables en su host para que actúe como enrutador y firewall para su huesped.

El enrutamiento se realiza simplemente configurando la ruta predeterminada en el cliente a la dirección IP del host, permitiendo el reenvío de IP y configurando una ruta al dispositivo tap del cliente en el host.


* Lado del host: permita el reenvío de IPv4 y agregue una ruta al huesped (se podría poner en un script, pero la ruta debe agregarse después de que el huesped haya comenzado):
~~~
sysctl -w net.ipv4.ip_forward=1 # permitir el reenvío de IPv4
ruta agregar -host ip-of-client dev tap-device # agregar ruta al cliente
~~~

* Lado del huesped: establezca la puerta de enlace predeterminada en la dirección IP del host (asegúrese de que las direcciones IP del host y del huesped estén en la misma subred):
~~~
route add default gw <ip-of-host>
~~~

* Nota: si el host no está en la misma subred que el huesped, debe agregar manualmente la ruta al host antes de crear la ruta predeterminada:
~~~
route add -host <ip-of-host> dev <network-interface>
route add default gw <ip-of-host>
~~~

VDE

Otra opción es usar VDE (Ethernet Virtual Distribuida).

Se proporcionará más información más adelante.
Actuación

Los datos sobre los resultados de la evaluación comparativa deben ir aquí. Ahora [hay una página](http://www.linux-kvm.org/page/Networking_Performance) dedicada a ideas para mejorar el rendimiento de la red.

Algunas comparaciones de rendimiento de NIC 10G entre VFIO passthrough y virtio se analizan en [VFIO vs virtio](http://www.linux-kvm.org/page/VFIO_vs_virtio).

### Compatibilidad

Hay otra sintaxis antigüa y obsoleta de especificar la red para máquinas virtuales. Los ejemplos anteriores usan el modelo -netdev..-device, la forma antigüa usaba pares -net..-net. Por ejemplo:
~~~
-netdev tap,id=net0 -dispositivo e1000,netdev=net0,mac=52:54:00:12:34:56
~~~

Es casi lo mismo que el viejo
~~~
-net tap,vlan=0 -net nic,vlan=0,model=e1000,macaddr=52:54:00:12:34:56
~~~
(tenga en cuenta el cambio de parámetro mac = macaddr también; vlan=0 es el valor predeterminado).

La forma antigua usaba la noción de 'VLAN': estas son QEMU VLANS, que no tienen nada que ver con las VLAN 802.1q. Las VLAN de Qemu se numeran a partir de 0 y es posible conectar uno o más dispositivos (ya sea del lado del host, como -net tap, o del lado del huesped, como -net nic) a cada VLAN y, en particular, es posible conectar más de 2 dispositivos a una VLAN. Cada dispositivo en una VLAN recibe todo el tráfico recibido por cada dispositivo en ella. Este modelo era muy confuso para el usuario (especialmente cuando un huesped tiene más de una NIC).

En el nuevo modelo, cada lado del host corresponde a un solo lado del huesped, formando un par de dispositivos basados ​​en los parámetros -netdev id= y -device netdev=. Es menos confuso, es más rápido (porque siempre es un par 1:1) y admite más parámetros que la forma antigua -net..-net.


Sin embargo, -net..-net todavía es compatible, se usa ampliamente y se menciona en muchos CÓMO y guías de todo el mundo. También es un poco más corto y más rápido de escribir.


## Construir un puente público con NetworkManager NMCLI

Escenario: 

Tarjeta de red "eno1" por cable



### Para ver las propiedades de las conexiones de red

~~~
    $ nmcli con show

    $ nmcli connection show --active
~~~

### Crear puente y conectarlo a "eno1"
~~~
$ sudo nmcli con add ifname br0 type bridge con-name br0
~~~
Vinculando el bridge a br0
~~~
$ sudo nmcli con add type bridge-slave ifname eno1 master br0
~~~
Iniciar el bridge bro
~~~
 nmcli con up br0
~~~

Inactivar el protocolo STP en el bridge
~~~
$ sudo nmcli con modify br0 bridge.stp no
$ nmcli con show
~~~

Para verificar la configuración incluyendo el estado del STP
~~~
$ nmcli -f bridge con show br0
~~~







## Almacenamiento

### Crear un volumen con dd
Nota: para crear una imagen como volumen usamos el comando: dd, que es un comando estándar de Unix, no de virsh ni qemu. Tal vez hay un comando para eso en virsh, pero al momento lo estamos realizando con dd.

Para realizar una imagen que contenga sólo ceros. Con un tamaño de bloque (bs) en 1024 y cuenta de 5MB, se crea una imagen de 5.1GB.

~~~
sudo dd if=/dev/zero of=/ruta/de/la/img/DebianVol1.img bs=1024 count=5MB
~~~


## Redimensionar y extender una imagen como disco virtual

1. Detener el dominio
~~~
sudo virsh destroy nombre_del_dominio
~~~

2. listar las particiones con el comando: `sudo virsh domblklist nombre_del_dominio`

3. Agregar espacio al archivo de imagen que se usa como disco virtual

`sudo qemu-img resize /mnt/hd2/MV/haacvd.img +5G`

3. Iniciar el dominio:

`sudo virsh start nombre_del_dominio`

4. Redimensionar el archivo de imagen al tamaño final
tiene que estar prendido el dominio y desde la consola (no dentro del dominio) de virsh ejecutar:

`sudo virsh blockresize nombre_del_dominio ruta_al_disco --size 10G`
`sudo virsh blockresize Debianipfija vdb --size 10G`

Ejemplo:
~~~
halvarez@pasteursoft-k8s:/mnt/hd2/MV$ sudo virsh blockresize FaiSrv /mnt/hd2/MV/FaiServer.qcow2  99G
El dispositivo de bloque '/mnt/hd2/MV/FaiServer.qcow2' cambió de tamaño
halvarez@pasteursoft-k8s:/mnt/hd2/MV$
~~~

5. Ingresar al dominio 

6. Correr el comando `lsblk` para ver el tamaño actual del disco

7. Extender la particion con el comando `cfdisk /dev/nombre_del_dev` y seleccionar extender, confirmar el tamaño de la particion, aceptar y escribir la configuración.

8. Ejecutar de nuevo el comando `lsblk` para confirmar el cambio del tamaño de la particion.

Verificar el sistema de archivos dentro del dominio:

 `e2fsck -f /dev/vdb1`

extender la partición:
 `resize2fs /dev/vdb1`

9. fin del procedimiento

### Crear volumen con qemu-img

sudo qemu-img create -f formato_tipo volumen.qcow2 tamaño


sudo qemu-img create -f qcow2 pasteur.qcow2 1G

hd pasteur 1G
pasnet 1G
gestor 2G
cliente 5G


### Convertir imagen raw.img a qcow2

qemu-img convert -f raw -O qcow2 volume.img volume.qcow2

qemu-img convert -f raw -O qcow2 pasteur.img pasteur.qcow2

### Incrementar el tamaño de una imagen de disco qcow2

~~~
sudo qemu-img resize pasnet_cliente_1.qcow2 +1G
~~~

### Agregar un volumen a una instancia virtual

Se puede quitar la definición del dominio y luego editar el xml, pero se sugiere mejor usar el comando: edit

Si es la primera vez que ejecuta el comando, probablemente solicitará la preferencia del editor que se va a usar: nano, vim, vim.tiny, ...

Luego se puede proceder a realizar la edición del xml para agregar un volumen insertando la sección siguiente cambiando los valores correspondientes:

<disk type='block' device='disk'>
  <driver name='qemu' type='raw'/>
  <source dev='/dev/md/storage'/>
  <target dev='vdb' bus='virtio'/>
</disk>

En el tipo puede ser block o file, pero como si no se van a anexar discos físicos por lo regular se usa file.

_Nota:_ Esto requiere del reinicio del dominio, no tiene un efecto de hotswap. Falta ver si es posible realizar eso y cómo.

_Nota para mi:_ Falta probar la anexión de un disco completo a una virtual.

## Referencias

http://www.linux-kvm.org/page/Networking
https://hazlolinux.com/kvm/como-crear-una-interfaz-de-puente-en-red-kvm-centos-8/
https://www.ochobitshacenunbyte.com/2014/09/30/configuracion-de-red-en-kvm-en-modo-public-bridge/
https://programmerclick.com/article/95222253540/

https://www.cyberciti.biz/faq/how-to-configuring-bridging-in-debian-linux/

https://computingforgeeks.com/how-to-install-kvm-virtualization-on-debian/

https://zepworks.com/posts/access-minikube-remotely-kvm/

https://h4ckseed.wordpress.com/2021/07/29/administrando-virtualizacion-kvm-con-virsh/

https://www.howtogeek.com/devops/how-to-increase-a-kvm-virtual-machines-disk-size/

https://access.redhat.com/documentation/es-es/red_hat_enterprise_linux/5/html/virtualization/ch-config-use-network

https://access.redhat.com/documentation/es-es/red_hat_enterprise_linux/8/html-single/configuring_and_managing_virtualization/index#viewing-storage-pool-information-using-the-web-console_viewing-vm-information-using-the-rhel-8-web-console
