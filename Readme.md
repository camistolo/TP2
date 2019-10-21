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

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_blinky.PNG)

#### Con Time Events

En el diagrama de estados con **Time Events**, los eventos que cambian de estado al LED son los siguientes eventos de tiempo:

    1. after 250 ms
    2. after 500 ms

Por esta razón, no se definió ningun evento en el cuadro de la izquierda.

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_blinkyte.PNG)

### Código en main.c

#### Inicializaciones

En primer lugar, se hacen todas las configuraciones de la placa, de los ticks y de las interrupciones como se vio en el primer TP. Con un **#define** se debe establecer si se usan **Time Events** o no. En el caso de que se utilicen los **Time Events**, se deben inicializar los **Timer Ticks**.

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_1.PNG)

Se realiza primero la inicialización del diagrama a través de la función **prefix_init** que define la estructura de datos de la máquina de estados y que llama a las funciones **prefix_ClearInEvents** y **prefix_ClearOutEvents**

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_2.PNG)

La función **prefix_ClearInEvents** define el evento como inicialmente apagado.

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_4.PNG)

Luego, la función **prefix_enter** setea la secuencia por defecto del programa, que a través de varias funciones que se mostrarán en las siguientes cinco imágenes, se puede ver que define el comienzo del diagrama en el estado **APAGADO**

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_6.PNG)

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_7.PNG)

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_8.PNG)

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_9.PNG)

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_10.PNG)

La función **prefix_enact_main_region_APAGADO** llama a la acción **prefixIface_opLED**, que el programador tuvo que definir y que se observa a continuación, donde se llama a la función **gpioWrite** que establece un estado en el LED que se le indica:

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_19.PNG)

La función principal es un loop que espera a que la interrupción ocurra, al igual que en el TP 1. La diferencia en este caso es si están definidos o no los **Time Events**.

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_11.PNG)

    1. Si los **Time Events** no están activados, se llamá a la función **prefixIface_raise_evTick**, que enciende el evento **ev_Tick** como se muestra a continuación:

    ![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_13.PNG)

    2. Si los **Time Events** están activados, se llamá a las función **UpdateTimers**, que define la estructura de los ticks, mostrada a continuación: 

    ![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_12.PNG)
    ![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_14.PNG)

    Luego, se llama a la función **IsPendEvent**, donde se chequea si hay algún evento en la cola de eventos, esperando a ocurrir.

    ![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_13.PNG)

    La función **prefix_raiseTimeEvent** funciona de forma parecida a la función **prefixIface_raise_evTick**, seteando el evento a uno.

    ![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_20.PNG)

    Por último, mediante la función **MarkAsAttEvent** se determina que el evento ya no se encuentra pendiente.

    ![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_16.PNG)

Por último, se llama a la función **prefix_runCycle** que permite la ejecución del ciclo que se creo en el diagrama de estados.

![](C:\Users\camis\Desktop\Camila\Fiuba\SistemasEmbebidos\TP2\Imagenes\1_18.PNG)





