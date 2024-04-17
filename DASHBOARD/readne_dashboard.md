# Dashboard

En este apartado se describiran los procesos que se hicieron para contruir el dashboard en power bi.

Iniciamos con la portada que tiene un boton para pasar a la hoja de objetivos.

La hoja de objetivos tiene descrito  los objetivos y 3 botones para ingresar a las hojas de cada kpi

Cada kpi tendra su propia hoja que son descritos a continuación:


# KPIs

Debes graficar y medir los 2 KPIs propuestos a continuación, representándolos adecuadamente en el dashboard. A su vez, tambíen tienes que proponer, medir y graficar un tercer KPI que consideres relevante para la temática. Los dos KPIs propuestos son:

## Reducir en un 10% la tasa de homicidios en siniestros viales de los últimos seis meses, en CABA(buenos aires), en comparación con la tasa de homicidios en siniestros viales del semestre anterior.
    palabras del cliente: Definimos a la tasa de homicidios en siniestros viales como el número de víctimas fatales en accidentes de tránsito por cada 100,000 habitantes en un área geográfica durante un período de tiempo específico. Su fórmula es: (Número de homicidios en siniestros viales / Población total) * 100,000

    o sea que se deben comparar solo los del añ0 2021 los de finales del año vs los del inicio del año.
    para esto debo de crear una tabla con los datos simados segun los primero 6 meses y luego el de los 2do seis meses y hacer una 
    
    * primero obtenemos la suma de los homicidios del primer semestre
    homicidios_1sem = CALCULATE(
    SUM('homicidiosclean_20y21'[N_VICTIMAS]),
    FILTER(
        'homicidiosclean_20y21',
        'homicidiosclean_20y21'[AAAA] = 2021 &&
        'homicidiosclean_20y21'[MM] <= 6
            )
        )

    * luego obtenemos la suma de los homicidios del segundo semestre
        homicidios_2sem = CALCULATE(
        SUM('homicidiosclean_20y21'[N_VICTIMAS]),
        FILTER(
            'homicidiosclean_20y21',
            'homicidiosclean_20y21'[AAAA] = 2021 &&
            'homicidiosclean_20y21'[MM] > 6
                )
            )

    * la ultima medicion seria de sumar el ambas y obtener el porcentaje de reduccion.

    homicidios_porcentual_anual = 
    CALCULATE(
         1-([homicidios_2sem]/[homicidios_1sem])
         )
    
    En el dashboard se mostrara el porcentaje de reducción por el dax
                homicidios_porcentual_anual = 
            CALCULATE(
            (1-([homicidios_2sem]/[homicidios_1sem])
            ))

    *para las tarjetas de los homicidios en el primer semestre y en el segundo semestre:   

        PoblacionTotal = 3121707
    
         Homicidios2por100k = 
        CALCULATE(
                [homicidios_2sem]/[PoblacionTotal] * 100000
                )


        Homicidios1por100k = 
        CALCULATE(
                [homicidios_1sem]/[PoblacionTotal] * 100000
                )

    * por último mostraremos la comparación entre los semestres en una grafico de stacked bars 100% y un mapa para localizar la comuna.




## Reducir en un 7% la cantidad de accidentes mortales de motociclistas en el último año, en CABA, respecto al año anterior.
     palabras del clientes: Definimos a la cantidad de accidentes mortales de motociclistas en siniestros viales como el número absoluto de accidentes fatales en los que estuvieron involucradas víctimas que viajaban en moto en un determinado periodo temporal. Su fórmula para medir la evolución de los accidentes mortales con víctimas en moto es: (Número de accidentes mortales con víctimas en moto en el año anterior - Número de accidentes mortales con víctimas en moto en el año actual) / (Número de accidentes mortales con víctimas en moto en el año anterior) * 100

MUY IMPORTANTE repasar qué es un KPI y cómo se diferencia de una métrica convencional. En el material de apoyo tienen lectura que puede ser de ayuda.
    Ocupamos los registros del años 2020 y 2021 de los homicidios viales.
    A estos registros se les agregara una columna que almacene los N_victimas del año 2020.
    A estos registros se les agregara una columna que almacene los N_victimas del año 2021.
    De estas para cada una de estas columnas se agregara una nueva columna donde se almacenen las victimas que sean moto, nombradas victimasMoto20 y victimasMoto21 respectivamente.

    Iniciamos con un slicer para indicar los años a trabajar.
    seguido con una tarjeta donde indicaremos el porcentaje de reducción que hubo en el año. el cual es un dax de nombre 'taza de disminucion para los homicidios a motos'  para obtener ese calculo se hizo lo siguiente

    Sumar los accidentes de motos en el 2020
        motos20 = 
            SUMX(
                FILTER(homicidiosclean_20y21,    //tabla
                homicidiosclean_20y21[AAAA]=2020 && homicidiosclean_20y21[VICTIMA] ="MOTO" // condiconales
                ), // fin del filtro
                homicidiosclean_20y21[N_VICTIMAS]          //COLUMNA A SUMAR
                )

    Sumar los accidentes de motos en el 2021
        motos21 = 
            SUMX(
                FILTER(homicidiosclean_20y21,    //tabla
                homicidiosclean_20y21[AAAA]=2021 && homicidiosclean_20y21[VICTIMA] ="MOTO" // condiconales
                ), // fin del filtro
                homicidiosclean_20y21[N_VICTIMAS]          //COLUMNA A SUMAR
                )

    calcular la taza de porcentaje 
        taza de disminucion para los homicidios a motos = 
            CALCULATE(1-([moto21pob]/[moto20pob]))

    Para indicar las calles con mayor cantidad de homicidios se uso clustered bar char, limitandolo a solo las 2 calles con mas incidencias.
    
    Mientras que un Map nos muestra la locación de los accidentes en estos años, y dado al slicer del principio podemos separar los puntos mostrados en el mapa.

    Al fondo a la izquierda tenemos un liner chart titulado "Victimas anuales" en el que podemos ver como fueron los siniestros en cada mes de cada año. 



## Reducir en un 15% la cantidad de accidentes mortales de peatones por parte de autos en el último año, en CABA, respecto al año anterior.

    Ocupamos los registros del años 2020 y 2021 de los homicidios viales.
        A estos registros se les agregara una columna que almacene los N_victimas del año 2020.
        A estos registros se les agregara una columna que almacene los N_victimas del año 2021.
        De estas para cada una de estas columnas se agregara una nueva columna donde se almacenen las victimas que sean auto20, autoypeaton20, auto21 y autoypeaton21 respectivamente.


Iniciamos con un slicer para indicar los años a trabajar.
    seguido con una tarjeta donde indicaremos el porcentaje de reducción que hubo en el año. el cual es un dax de nombre 'taza de disminucion para los homicidios a motos'  para obtener ese calculo se hizo lo siguiente

    Sumar los homicidios en el 2020
        homicidios victima peatones y acusado autos20 = 
            SUMX(
                FILTER(homicidiosclean_20y21,    //tabla
                homicidiosclean_20y21[AAAA]=2020 && homicidiosclean_20y21[VICTIMA] ="PEATON" && homicidiosclean_20y21[ACUSADO] ="AUTO" // condiconales
                ), // fin del filtro
                homicidiosclean_20y21[N_VICTIMAS]          //COLUMNA A SUMAR

    Sumar los accidentes de motos en el 2021
        homicidios victima peatones y acusado autos21 = 
            SUMX(
                FILTER(homicidiosclean_20y21,    //tabla
                homicidiosclean_20y21[AAAA]=2021 && homicidiosclean_20y21[VICTIMA] ="PEATON" && homicidiosclean_20y21[ACUSADO] ="AUTO" // condiconales
                ), // fin del filtro
                homicidiosclean_20y21[N_VICTIMAS]          //COLUMNA A SUMAR
                )

    calcular la taza de porcentaje disminuido 
       taza de disminucion para los homicidios a PEATONES por AUTOS = 
        CALCULATE(1-([homicidios victima peatones y acusado autos21]/[homicidios victima peatones y acusado autos20]))


    Para indicar las calles con mayor cantidad de homicidios se uso clustered bar char, limitandolo a solo las 3 calles con mas incidencias.
    
    Mientras que un Map nos muestra la locación de los accidentes en estos años, y dado al slicer del principio podemos separar los puntos mostrados en el mapa.

    Al fondo a la izquierda tenemos un staked column chart titulado "Victimas anuales" en el que podemos ver como fueron los siniestros en cada mes de cada año. 


    
