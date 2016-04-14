<!--author=SharS last changed: 9/17/15-->

#### Para instalar actualizaciones en el modo de mantenimiento a través de Windows PowerShell para StorSimple

1. Si aún no lo hizo, acceda a la consola serie del dispositivo y seleccione la opción 1, **Iniciar sesión con acceso completo**. 

2. Escriba la contraseña. La contraseña predeterminada es **Password1**.

3. En el símbolo del sistema, escriba:

     `Get-HcsUpdateAvailability`
    
4. Se le notificará si hay actualizaciones disponibles y si son problemáticas. Para aplicar actualizaciones problemáticas, deberá poner el dispositivo en modo de mantenimiento. Consulte [Paso 2: Acceso al modo de mantenimiento](storsimple-update-device.md#step2) para obtener instrucciones.

5. Cuando el dispositivo se encuentre en modo de mantenimiento, en el símbolo del sistema, escriba: `Start-HcsUpdate`

6. Se le pedirá confirmación. Después de confirmar las actualizaciones, se instalarán en el controlador al que esté accediendo en ese momento. Una vez instaladas las actualizaciones, se reiniciará el controlador.

7. Supervise el estado de las actualizaciones. Escriba:

	`Get-HcsUpdateStatus`
	
	Si `RunInProgress` es `True`, la actualización está en curso. Si `RunInProgress` es `False`, indica que se ha completado la actualización.

7. Cuando la actualización está instalada en el controlador actual y se ha reiniciado, conéctese a otro controlador y realice los pasos del 1 al 6.

8. Una vez actualizados ambos controladores, salga del modo de mantenimiento. Consulte [Paso 4: Salida del modo de mantenimiento](storsimple-update-device.md#step4) para obtener instrucciones.

<!---HONumber=Oct15_HO3-->