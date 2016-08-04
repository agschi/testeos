<properties
    pageTitle="Site Recovery (VMware to Azure)/Reprotect VM after failover"
    description="Site Recovery (de VMware a Azure)/proteger máquina virtual después de conmutación por error"
    service="microsoft.recoveryservices"
    resource="vaults"
    authors="aashu"
    displayOrder=""
    selfHelpType="generic"
    supportTopicIds="32536447"
    resourceTags=""
    productPesIds="15207"
    cloudEnvironments="public"
/>


# Site Recovery (de VMware a Azure)/proteger máquina virtual después de conmutación por error

Problemas comunes durante la conmutación por recuperación o la reprotección
## **Pasos recomendados**

* La cuenta de vCenter usada para la detección debe tener los permisos adecuados para la conmutación por recuperación <br>
[Consulte la lista de permisos aquí](https://aka.ms/asrsupfailbackperm)

* La licencia de vCenter no debe ser una licencia gratuita: debe ser de evaluación o con licencia. <br>
En el nodo de host ESXi en vCenter, vaya a la pestaña Configuración -> Licensed features (Características con licencia). vSphere API debe aparecer en las características del producto. La máquina virtual conmutada por error debe estar en la red correcta.

* La máquina virtual se debe estar ejecutando y estar en una red para poder comunicarse con el servidor de configuración y el servidor de procesos. <br>
Compruebe si la máquina virtual está en la red de Azure conectada a ExpressRoute o VPN.

* Los almacenes de datos que contengan un disco de la máquina virtual se deben montar en el host ESXi de los destinos principales <br>
Asegúrese de que el almacén de datos está montado como de lectura y escritura y es de tipo VMFS. Actualmente no se admiten otros tipos de almacén de datos.

* Si la unidad de retención no está visible en el menú de selección, agregue una nueva unidad al destino principal<br>
    1. El volumen no puede utilizarse para ningún otro propósito (destino de replicación, etc.).
    2. El volumen no debe estar en modo de bloqueo.
    3. El volumen no debe ser el volumen de memoria caché. (La instalación del destino maestro no debe existir en dicho volumen. El volumen de instalación personalizada del servidor de procesos y el destino maestro no es apto para el volumen de retención. Aquí el volumen del servidor de procesos y el destino maestro es el volumen de memoria caché del destino maestro. )
    4. El tipo de sistema de archivos de volumen no debe ser FAT ni FAT32.
    5. La capacidad del volumen debe ser distinta de cero. e. El volumen de retención predeterminado para Windows es el volumen R.
    6. El volumen de retención predeterminado para Linux es /mnt/retention.

* [Aquí también se mostrarán algunos problemas comunes](https://aka.ms/asrsupfailbackcommonissues)

## **Documentos recomendados**
La [documentación completa sobre la conmutación por recuperación está aquí](https://aka.ms/asrsupv2afailback)



<!--HONumber=Jul16_HO4-->

