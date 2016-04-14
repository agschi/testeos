<properties 
   pageTitle="Grupos de volúmenes de Administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento MMC para Administrador de instantáneas StorSimple para crear y administrar grupos de volúmenes."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/02/2015"
   ms.author="v-sharos" />

# Uso de Administrador de instantáneas StorSimple para crear y administrar grupos de volúmenes

## Información general

Puede usar el nodo **Grupos de volúmenes** en el panel **Ámbito** para asignar volúmenes a los grupos de volúmenes, ver información acerca de un grupo de volúmenes, programar copias de seguridad y editar grupos de volúmenes.

Los grupos de volúmenes son grupos de volúmenes relacionados que se usan para asegurarse de que las copias de seguridad son coherentes con las aplicaciones. Para obtener más información, consulte [Volúmenes y grupos de volúmenes](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [Integración con el Servicio de instantáneas de volumen de Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT]
>
> * Todos los volúmenes de un grupo de volúmenes deben provenir de un solo proveedor de servicios en la nube.
> 
> * Al configurar grupos de volúmenes, no mezcle los volúmenes compartidos de clúster (CSV) con los que no lo son en el mismo grupo de volúmenes. Administrador de instantáneas StorSimple no admite una mezcla de CSV y no CSV en la misma instantánea.
 
![Nodo Grupos de volúmenes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Ilustración 1: Nodo de grupos de volúmenes de Administrador de instantáneas StorSimple**

Este tutorial explica cómo puede usar Administrador de instantáneas StorSimple para:

- Visualizar información acerca de los grupos de volúmenes 
- Crear un grupo de volúmenes
- Realizar una copia de seguridad de un grupo de volúmenes
- Editar un grupo de volúmenes
- Eliminar un grupo de volúmenes

También tiene todas estas acciones disponibles en el panel **Acciones**.
 
## Visualización de grupos de volúmenes

Si hace clic en el nodo **Grupos de volúmenes**, el panel **Resultados** muestra la siguiente información acerca de cada grupo de volúmenes, según las columnas que seleccione. (Las columnas del panel **Resultados** son configurables. Haga clic con el botón derecho en el nodo **Volúmenes**, seleccione **Ver** y, a continuación, seleccione **Agregar o quitar columnas**).

Columna Resultados | Descripción 
:--------------|:------------ 
Nombre | La columna **Nombre** contiene el nombre del grupo de volúmenes.
Application | La columna **Aplicaciones** muestra el número de los escritores VSS actualmente instalados y en ejecución en el host de Windows.
Seleccionado | La columna **Seleccionados** muestra el número de volúmenes que se encuentran en el grupo de volúmenes. Un cero (0) indica que ninguna aplicación está asociada con los volúmenes en el grupo de volúmenes.
Importado | La columna **Importados** muestra el número de volúmenes importados. Cuando se establece en **True**, esta columna indica que un grupo de volúmenes se importó desde el Portal de Azure clásico y que no se creó en StorSimple Snapshot Manager.
 
>[AZURE.NOTE]Asimismo, también se muestran los grupos de volúmenes de StorSimple Snapshot Manager en la pestaña **Directivas de copia de seguridad** en el Portal de Azure clásico.
 
## Crear un grupo de volúmenes

Utilice el procedimiento siguiente para crear un grupo de volúmenes.

#### Para crear un grupo de volúmenes

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple. 

2. En el panel **Ámbito**, haga clic con el botón derecho en **Grupos de volúmenes** y, a continuación, haga clic en **Crear grupo de volúmenes**.

    ![Crear grupo de volúmenes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Aparecerá el cuadro de diálogo **Crear grupo de volúmenes**.

    ![Cuadro de diálogo Crear un grupo de volúmenes](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)

3.  Escriba la siguiente información:

    1. En el cuadro **Nombre**, escriba un nombre único para el nuevo grupo de volúmenes. 

    2. En el cuadro **Aplicaciones**, seleccione las aplicaciones asociadas a los volúmenes que va a agregar al grupo de volúmenes.

        Recuerde que el cuadro **Aplicaciones** solo enumera las aplicaciones que usan volúmenes de Azure StorSimple y que tienen los escritores VSS habilitados. Un escritor de VSS está habilitado solo si todos los volúmenes que reconozca el sistema de escritura son volúmenes Azure StorSimple. Si el cuadro de aplicaciones está vacío, quiere decir que no hay instaladas aplicaciones que utilicen volúmenes Azure StorSimple y tengan instalados escritores VSS admitidos. (Actualmente, Azure StorSimple admite Microsoft Exchange y SQL Server). Para obtener más información acerca de los escritores VSS, consulte [Integración con el Servicio de instantáneas de volumen de Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Si selecciona una aplicación, se seleccionan automáticamente todos los volúmenes asociados a ella. Por el contrario, si selecciona los volúmenes asociados a una aplicación específica, la aplicación se selecciona automáticamente en el cuadro **Aplicaciones**.

    3. En el cuadro **Volúmenes**, seleccione los volúmenes Azure StorSimple que agregará al grupo de volúmenes.

      - Puede incluir volúmenes con una o varias particiones. (Los volúmenes de varias particiones pueden ser discos dinámicos o discos básicos con varias particiones.) Un volumen que contiene varias particiones se trata como una sola unidad. Por lo tanto, si agrega solo una de las particiones a un grupo de volúmenes, todas las demás particiones se agregan automáticamente a ese grupo de volúmenes al mismo tiempo. Después de agregar un volumen de varias particiones a un grupo de volúmenes, el volumen de varias particiones continúa tratándose como una sola unidad.

      - Puede crear grupos de volúmenes vacíos no asignándoles volúmenes.

      - No mezcle los volúmenes compartidos de clúster (CSV) y no CSV en el mismo grupo de volúmenes. Administrador de instantáneas StorSimple no admite una mezcla de volúmenes CSV y no CSV en la misma instantánea.

4. Haga clic en **Aceptar** para guardar el grupo de volúmenes.

## Realizar una copia de seguridad de un grupo de volúmenes

Utilice el procedimiento siguiente para realizar la copia de un grupo de volúmenes.

#### Para realizar una copia de seguridad de un grupo de volúmenes

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.

2. En el panel **Ámbito**, expanda el nodo **Grupos de volúmenes**, haga clic con el botón derecho en el nombre de un grupo de volumen y, a continuación, haga clic en **Realizar copia de seguridad**.

    ![Copia de seguridad de un grupo de volúmenes de forma inmediata](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. En el cuadro de diálogo **Realizar copia de seguridad** seleccione **Instantáneas locales** o **Instantáneas de nube** y, a continuación, haga clic en **Crear**.

    ![Cuadro de diálogo Realizar copia de seguridad](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)

4. Para confirmar que se está ejecutando la copia de seguridad, expanda el nodo **Trabajos** y, a continuación, haga clic en **En ejecución**. Tendría que aparecer la copia de seguridad.

5. Para ver la instantánea completa, expanda el nodo **Catálogo de copia de seguridad**, expanda el nombre del grupo de volúmenes y, a continuación, haga clic en **Instantáneas locales** o en **Instantáneas de nube**. La copia de seguridad se mostrarán si se finalizó correctamente.

## Edición de un grupo de volúmenes

Utilice el procedimiento siguiente para editar un grupo de volúmenes.

#### Para editar un grupo de volúmenes

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.

2. En el panel **Ámbito**, expanda el nodo **Grupos de volúmenes**, haga clic con el botón derecho en el nombre de un grupo de volumen y, a continuación, haga clic en **Editar**.

3. Aparece el cuadro de diálogo **Crear un grupo de volúmenes**. Puede cambiar las entradas **Nombre**, **Aplicaciones** y **Volúmenes**.

4. Haga clic en **Aceptar** para guardar los cambios.

## Eliminación de un grupo de volúmenes

Utilice el procedimiento siguiente para eliminar un grupo de volúmenes.

>[AZURE.WARNING]Esto también elimina todas las copias de seguridad asociadas con el grupo de volúmenes.

#### Para eliminar un grupo de volúmenes

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple. 

2. En el panel **Ámbito**, expanda el nodo **Grupos de volúmenes**, haga clic con el botón derecho en el nombre de un grupo de volumen y después haga clic en **Eliminar**.

3. Aparecerá el cuadro de diálogo **Eliminar grupo de volúmenes**. Escriba **Confirmar** en el cuadro de texto y, a continuación, haga clic en **Aceptar**.

    El grupo de volúmenes eliminado desaparecerá de la lista del panel **Resultados** y todas las copias de seguridad asociadas a ese grupo de volúmenes se eliminan desde el catálogo de copia de seguridad.

## Pasos siguientes

- Obtenga más información sobre el [uso de Snapshot Manager de StorSimple para administrar la solución de StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga más información sobre el [uso de Snapshot Manager de StorSimple para crear y administrar las directivas de copia de seguridad](storsimple-snapshot-manager-manage-backup-policies.md).

<!---HONumber=AcomDC_1203_2015-->