# Repositorio del TP2 - Camila Stolowicz

## Yakindu

Yakindu es una herramienta que permite la escritura de código en diagramas de estado. Éstos se crean a partir de estados, eventos, acciones y variables. La ventaja de esta herramienta recae en el beneficio visual que genera, ya que permite simular el diagrama creado y ver el funcionamiento, corrigiéndolo si es necesario. 
Una vez creado el diagrama final, el mismo programa genera el código correspondiente y permite el posterior debuggeo, sólo requiriendo que las acciones sean codificadas en un archivo de tipo **.c** (por ejemplo, el main.c).

### Tipos de archivos 

#### Archivo .sct
 El archivo donde se genera el diagrama de estados es de este tipo. Aquí se definen los eventos, acciones y variables y toda la conexión que hay entre ellos. Haciendo clic derecho en el nombre del archivo y seleccionando **Run As → Statechart Simulation** comienza la simulación. En la solapa de **Simulación**, se pueden modificar los eventos y los valores de las variables.

 En el archivo **.sct** se van a crear los bloques de código y luego, se definirán los eventos, acciones y variables en un cuadro a la izquierda del archivo. Dentro del cuadro, se dividirán los parámetros correspondientes a la interfaz por un lado y por el otro estarán los internos, que no reciben entradas ni envían salidas, sino que pertenecen a operaciones que se hacen dentro del código.

#### Archivo .sgen

Este archivo permite la creación de código en **.c** a partir del diagrama de estados. Una vez que se finalizó el diagrama, haciendo clic derecho en el archivo **.sgen**, seleccionando la opción **Generate Code Artifacts**, se crearán los archivos de tipo **.c** y **.h** correspondientes, que permitirán pasar el código compilado a la placa. 

### Time Events
Yakindu trabaja con eventos, que permiten el paso de un estado a otro. Estos eventos pueden ser de dos tipos:

    1. Creados por el programador
    2. De tiempo

Los primeros, consisten en eventos que define el programador y pueden estar asociados con algún valor de entrada, por ejemplo. Los segundos son eventos que posee Yakindu y que se manejan con tiempo. Algunos ejemplos son:

    → every 1 s
    → after 250 ms
    → always

Para poder utilizarlos, se debe definir en un código aparte que se están utilizando **Time Events**. Se verá más adelante qué implica esta definción y qué debe aclararse.

## Archivo Blinky

El archivo **Blinky** consiste en hacer titilar un led. A continuación se verán los diagramas de estado correspondientes al archivo que no usa **Time Events** y al que sí los usa, y luego se mostrará el código que acompaña al diaggrama de estado, donde se definen las acciones y demás.

### Diagramas de estado

#### Sin Time Events

El diagrama de estado sin **Time Events** consiste en prender y apagar un LED cada vez que ocurra el evento **evTick**. La acción opLED, tanto en este caso como en el caso que utiliza **Time Events** será especificada en el archivo **main.c**

Las constantes que se definen son:

    1. El LED que se utiliza (en este caso, es el **LED3**)
    2. Los estados del LED (**LED_ON**, **LED_OFF**)

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_blinky.PNG?raw=true)

#### Con Time Events

En el diagrama de estados con **Time Events**, los eventos que cambian de estado al LED son los siguientes eventos de tiempo:

    1. after 250 ms
    2. after 500 ms

Por esta razón, no se definió ningun evento en el cuadro de la izquierda.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_blinkyte.PNG?raw=true)

### Código en main.c

#### Inicializaciones

En primer lugar, se hacen todas las configuraciones de la placa, de los ticks y de las interrupciones como se vio en el primer TP. Con un **#define** se debe establecer si se usan **Time Events** o no. En el caso de que se utilicen los **Time Events**, se deben inicializar los **Timer Ticks**.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_1.PNG?raw=true)

Se realiza primero la inicialización del diagrama a través de la función **prefix_init** que define la estructura de datos de la máquina de estados y que llama a las funciones **prefix_ClearInEvents** y **prefix_ClearOutEvents**

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_2.PNG?raw=true)

La función **prefix_ClearInEvents** define el evento como inicialmente apagado.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_4.PNG?raw=true)

Luego, la función **prefix_enter** setea la secuencia por defecto del programa, que a través de varias funciones que se mostrarán en las siguientes cinco imágenes, se puede ver que define el comienzo del diagrama en el estado **APAGADO**

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_6.PNG?raw=true)

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_7.PNG?raw=true)

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_8.PNG?raw=true)

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_9.PNG?raw=true)

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_10.PNG?raw=true)

La función **prefix_enact_main_region_APAGADO** llama a la acción **prefixIface_opLED**, que el programador tuvo que definir y que se observa a continuación, donde se llama a la función **gpioWrite** que establece un estado en el LED que se le indica:

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_19.PNG?raw=true)

La función principal es un loop que espera a que la interrupción ocurra, al igual que en el TP 1. La diferencia en este caso es si están definidos o no los **Time Events**.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_11.PNG?raw=true)

1. Si los **Time Events** no están activados, se llamá a la función **prefixIface_raise_evTick**, que enciende el evento **ev_Tick** como se muestra a continuación:

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_17.PNG?raw=true)

2. Si los **Time Events** están activados, se llamá a las función **UpdateTimers**, que define la estructura de los ticks, mostrada a continuación: 

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_12.PNG?raw=true)
![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_14.PNG?raw=true)

Luego, se llama a la función **IsPendEvent**, donde se chequea si hay algún evento en la cola de eventos, esperando a ocurrir.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_13.PNG?raw=true)

La función **prefix_raiseTimeEvent** funciona de forma parecida a la función **prefixIface_raise_evTick**, seteando el evento a uno.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_20.PNG?raw=true)

Por último, mediante la función **MarkAsAttEvent** se determina que el evento ya no se encuentra pendiente.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_16.PNG?raw=true)

Por último, se llama a la función **prefix_runCycle** que permite la ejecución del ciclo que se creo en el diagrama de estados.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/1_18.PNG?raw=true)

## Puerta Corrediza

Se implemento el modelo de control de una puerta corrediza automatizada, con movimientos en ambos sentidos, sensor de presencia y finales de carrera.
Se vana utilizar los siguientes componentes para simular el comprotamiento

     → Tecla 1: Sensor de presensia ON
     → Tecla 2: Sensor de presensia OFF
     → Tecla 3: Final de carrera Abierto
     → Tecla 4: Final de carrera Cerrado
     → Led 1: Abriendo
     → Led 2: Cerrando

Cuando se presione el pulsador 1 la puerta comenzará a abrirce hasta que se presione el final de carrera, es decir la tecla 3. Cuando se pulse la tecla 2, la puerta comenzará a cerrarce hasta que se presione el final de carrera Cerrado (tecla 4). Durante la abertura y cierre de la puerta, el LED 1 y LED 2, respectivamente, parpadearan hasta que lleguen al final del trayecto.

### Diagrama de estados

Los eventos, acciones, estados y constantes que se utilizarón para la implementación fueron los siguientes

![](https://github.com/camistolo/TP2/blob/master/Imagenes/pc_6.PNG)

En primer lugar se realizó el siguiente diagrama de estados, que es el encargado de describir la lógica principal del programa.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/pc_4.PNG)

Para realizar el sensado de las teclas se utilizo el siguiente diagrama de estados. En este, cuando se produzca el evento evTECXOprimido esperará 50 ms y se realizará la validación comprobando que se produzca ese mismo evento. De ser así se guarda el valor del pulsador para ver que tarea se debe realizar. En caso de que haya detectado incorrectamente que se pulso una tecla, vuelve al estado no oprimido.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/pc_2.PNG)

Una vez que se obtiene la información sobre que pulsador se presionó, IDLE la procesa. En esta region se espera recibir el dato mencionado anteriormente. Cuando se recibe alguno de estos valores se produce un evento, tal que la lógica principal actue.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/pc_3.PNG)

Luego de producirce cada evento se vuelve a esperar hasta que se presione otro pulsador para generar otro evento.

Para realizar el parpadeo de LEDs se generan eventos siTitilarLEDx, donde el LED x se mantendra apagado 250 ms y 500 ms prendido, hasta que aparezca el evento siNoTitilarLEDx, como se muestra en la siguiente figura.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/pc_5.PNG)


### Código de main.c

Una vez elegido el programa a ejecutar por medio de la compilación condicional, vamos a la porción de codigo correspondiente. En primier lugar, se puede observar la función Buttons_GetStatus_ encargada de detectar que tecla fue precionada.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/puerta1.PNG)

Luego, como se observa en la figura siguiente, se realiza la configuración de la placa y la inicialización de variables y estados.


## Generador de Señales

La consigna para el generador de señales era la siguiente:

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_0.PNG?raw=true)

Se determinó que para que se observen y controlen los cambios del generador, se iban a realizar las siguientes operaciones:

     → La forma de la señal se controla con la Tecla 1 y cada forma tiene asociado un color del LED RGB diferente:
        → Triangular: roja
        → Cuadrada: verde
        → Senoidal: azul
    → Cuando el LED1 está encendido, se puede modificar la tensión y cuando está apagado, la frecuencia. Esto se controla con la Tecla 2.
    → Para aumentar la magnitud correspondiente, se tiene que presionar la Tecla 3 y cada vez que esto se haga, se va a cambiar el LED2.
    → Para disminuir la magnitud, ocurre lo mismo que cuando se quiere aumentar pero con la Tecla 4 y el LED3.
    → Para salir de la configuración de cada magnitud (aumento o disminución), se tiene que presionar la Tecla 2.

### Diagrama de estados

Los eventos, acciones, estados y constantes son los establecidos en la consigna, como se puede observar a continuación.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_4.PNG?raw=true)

Se trabajó con teclas, por lo cual se utilizó el diagrama que lee si alguna tecla fue presionada, como en los ejercicios anteriores.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_8.PNG?raw=true)

La región principal, espera a que las teclas sean presionadas y si se presiona alguna, hace la acción correspondiente, hasta recibir otra señal de una tecla, como se observa a continuación:

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_6.PNG?raw=true)

En cuanto a la forma, se puede ver en la siguiente región que cada vez que se presione la Tecla 1, como indica la imagen anterior, se va a activar el evento **eForma** que va a entrar al estado de forma de señal correspondiente. En cada estado, se activa la acción **aSetForma**, que recibe una constante cuyo número está asociado con el color de LED de cada forma de señal:

    → cTRIANG: 0
    → cCUADR: 1
    → cSENOID: 2

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_5.PNG?raw=true)

La acción se define en el archivo **main.c** de la siguiente manera:

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_1.PNG?raw=true)

Se puede ver que la función recibe el valor del LED que se quiere prender, se enciende, luego se genera un delay y se vuelve a apagar.

En cuanto a la magnitud, se puede observar en la siguiente imagen que cada vez que ingresa al estado **TENSIÓN** o **FORMA**, se llama a la acción **aSetMagn**. 

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_7.PNG?raw=true)

La acción **aSetMagn** se define en el **main.c** de la siguiente forma:

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_2.PNG?raw=true)

Lo que esta acción hace es cambiar el valor del LED, según el valor que recibe. Es decir, según las siguientes constantes, definidas en la máquina de estado:

    → cTENS: true
    → cFREC: false

la función enciende el LED1 o lo apaga. Así, como se dijo antes, cuando se modifique la tensión, el LED1 va a estar prendido. Y para cambiar el valor de la frecuencia, se tendrá que apagar el LED1.

Para incrementar o disminuir las magnitudes, se utilizaron los eventos **eUp** y **eDown**, correspondientes cada uno a una tecla diferente (Tecla 3 y Tecla 4) y se implementaron las acciones **aIncTens** y **aIncFrec** que son equivalentes, al igual que **aDecTens** y **aDecFrec**.

![](https://github.com/camistolo/TP2/blob/master/Imagenes/gs_3.PNG?raw=true)

Se puede notar que las acciones que incrementan el valor de la magnitud son iguales y las que lo disminuyen, también. Por esta razón, podría considerarse eliminar las operaciones equivalentes y utilizar una sola función para cada acción, con nombre **aIncMagn** y **aDecMagn**.
Las acciones que aumentan el valor de la magnitud correspondiente, cambian el estado del LED2 cada vez que la Tecla 3 se presiona. Las operaciones que disminuyen su valor son iguales, sólo que modifican el LED3 cuando la Tecla 4 se presiona.
