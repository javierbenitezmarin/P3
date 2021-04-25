PAV - P3: detección de pitch
============================

Esta práctica se distribuye a través del repositorio GitHub [Práctica 3](https://github.com/albino-pav/P3).
Siga las instrucciones de la [Práctica 2](https://github.com/albino-pav/P2) para realizar un `fork` de la
misma y distribuir copias locales (*clones*) del mismo a los distintos integrantes del grupo de prácticas.

Recuerde realizar el *pull request* al repositorio original una vez completada la práctica.

Ejercicios básicos
------------------

- Complete el código de los ficheros necesarios para realizar la detección de pitch usando el programa
  `get_pitch`.

   * Complete el cálculo de la autocorrelación e inserte a continuación el código correspondiente.

	![imagen](https://user-images.githubusercontent.com/80445330/115998590-367ba280-a5e8-11eb-9d4e-0ea20f443cb8.png)
	Esta es una manera efectiva de implementar la función típica de autocorrelación. 
	
   * Inserte una gŕafica donde, en un *subplot*, se vea con claridad la señal temporal de un segmento de
     unos 30 ms de un fonema sonoro y su periodo de pitch; y, en otro *subplot*, se vea con claridad la
	 autocorrelación de la señal y la posición del primer máximo secundario.

	 NOTA: es más que probable que tenga que usar Python, Octave/MATLAB u otro programa semejante para
	 hacerlo. Se valorará la utilización de la librería matplotlib de Python.
	
	![image](https://user-images.githubusercontent.com/80445439/115993480-37560980-a5d3-11eb-9348-91d902756a0c.png)

	Como podemos observar en las gráficas, hemos usado un segmento de 30 ms con un fonema sonoro (concretamente, el fonema /m/) y lo hemos representado en Python, con la librería *matplotlib*. Después hemos hecho subplot de su autocorrelación y hemos encontrado el primer máximo en 6.99 ms, lo que significa que, según este criterio, **la frecuencia pitch es de 143 Hz** aproximadamente.
	
   * Determine el mejor candidato para el periodo de pitch localizando el primer máximo secundario de la
     autocorrelación. Inserte a continuación el código correspondiente.
	
	![imagen](https://user-images.githubusercontent.com/80445330/115998604-485d4580-a5e8-11eb-8357-b4a2347294bb.png)
	
	Este algoritmo recorre los valores de la autocorrelación desde un mínimo hasta un máximo decididos arbitrariamente. Y guarda el valor del índice de la autocorrelación donde encuentre el máximo local.
	
   * Implemente la regla de decisión sonoro o sordo e inserte el código correspondiente.
   
	El primer criterio que hemos usado para decidir si un segmento es sordo o sonoro ha sido el cálculo de r[1]/r[0], que es el cociente del segundo valor de la autocorrelación entre la potencia de la señal, es decir, se comparan dos muestras muy próximas. En nuestro caso, ese valor está guardado en la variable *r1norm*. 
	
	![image](https://user-images.githubusercontent.com/80445439/115993729-6456ec00-a5d4-11eb-90be-1a53f1c0dcf4.png)


Como se observa, asignamos el umbral decsior a 0.95; es decir, si *r1norm* supera este valor, asumiremos que es una trama sonora, y si no lo supera, una trama sorda. 

Usando únicamente este criterio, tenemos un score del 80%, lo que nos indica que tenemos que mejorar este criterio, como realizaremos más adelante.


- Una vez completados los puntos anteriores, dispondrá de una primera versión del detector de pitch. El 
  resto del trabajo consiste, básicamente, en obtener las mejores prestaciones posibles con él.

  * Utilice el programa `wavesurfer` para analizar las condiciones apropiadas para determinar si un
    segmento es sonoro o sordo. 
	
	  - Inserte una gráfica con la detección de pitch incorporada a `wavesurfer` y, junto a ella, los 
	    principales candidatos para determinar la sonoridad de la voz: el nivel de potencia de la señal
		(r[0]), la autocorrelación normalizada de uno (r1norm = r[1] / r[0]) y el valor de la
		autocorrelación en su máximo secundario (rmaxnorm = r[lag] / r[0]).
	
		Puede considerar, también, la conveniencia de usar la tasa de cruces por cero.

	    Recuerde configurar los paneles de datos para que el desplazamiento de ventana sea el adecuado, que
		en esta práctica es de 15 ms.
	
	Para conseguir los valores que nos interesan de cada trama (r1norm, pot y rmaxnorm), decidimos guardarlos en un fichero llamado 'features.txt' cada vez que se invocaba desde el programa principal a la función que calcula estos valores. De esta forma, podemos visualizar en el wavesurfer, la forma de onda de la imagen, el controno de pitch, y en otro panel, estas tres medidas superpuestas. Utilizando la rata, podemos hacer un estudio de cuáles son los valores que determinan cuando una trama es sonora o sorda.
	
	![imagen](https://user-images.githubusercontent.com/80445330/115998813-5495d280-a5e9-11eb-8851-67d9ae2ea915.png)

	En el panel superior tenemos la potencia de cada trama (en color negro), rmaxnorm (en color rojo) y r1norm (en color azul). Al haberlos puesto todos en un mismo panel, como las dos últimas características son normalizadas, quedan poco visibles, pero esto no es un problema, ya que el wavesurfer nos da el valor de cada una cuando pasamos la rata por encima del punto que nos interesa. De esta forma hemos podido determinar unos valores aproximados para mejorar nuestro sistema de detección.
      - Use el detector de pitch implementado en el programa `wavesurfer` en una señal de prueba y compare
	    su resultado con el obtenido por la mejor versión de su propio sistema.  Inserte una gráfica
		ilustrativa del resultado de ambos detectores.
  
  * Optimice los parámetros de su sistema de detección de pitch e inserte una tabla con las tasas de error
    y el *score* TOTAL proporcionados por `pitch_evaluate` en la evaluación de la base de datos 
	`pitch_db/train`.
	
	
	Como hemos indicado anteriormente, usando *únicamente* el criterio de *r1norm* no teníamos unos buenos resultados. Así que, para mejorar el sistema, decidimos usar 3 criterios: potencia, *r1norm* y *rmaxnorm*. Primero, comprobamos si la potencia es mayor de -10, luego si *r1norm* es mayor de 0.95 y, finalmente, si *rmaxnorm* es mayor de 0.5.
	
	![imagen](https://user-images.githubusercontent.com/80445330/115998557-11872f80-a5e8-11eb-84af-b79a766bd3e0.png)

	Cuando hemos comprobado los tres criterios, afirmaremos que la trama es sonora, si cumple, al menos, dos de esos tres criterios, y, con los umbrales adecuados, llegamos a un 89% en el score final. Como vemos ya es un resultado bueno, pero decidimos añadir un criterio más para mejorarlo.
	
	Cambiaremos el valor máximo de la frecuencia de pitch a elegir, es decir, si la frecuencia de pitch elegida es mayor que 375 Hz, decidiremos *unvoiced*. 
	
	![image](https://user-images.githubusercontent.com/80445439/115994217-573afc80-a5d6-11eb-8a72-e4389be864a9.png)

	Con esta última mejora, conseguimos un score del 90.58%, que es un valor muy adecuado para nuestro sistema.

	![image](https://user-images.githubusercontent.com/80445439/115994245-76d22500-a5d6-11eb-81dc-3cfa06ba4a5c.png)	
	
	
   * Inserte una gráfica en la que se vea con claridad el resultado de su detector de pitch junto al del
     detector de Wavesurfer. Aunque puede usarse Wavesurfer para obtener la representación, se valorará
	 el uso de alternativas de mayor calidad (particularmente Python).
  	El resultado final es este:
	
	![imagen](https://user-images.githubusercontent.com/80445330/115998905-dbe34600-a5e9-11eb-9c8e-592f3e99076e.png)

	El pitch detectado por nuestro programa es el que está en el panel superior. Si lo comparamos con la detección que hace wavesurfer, podemos ver que se acerca mucho. Si nos decidiéramos por implementar medidas de mejora, como un pre y post-procesado, el programa cada vez se acercaría más a unos resultados mejores. 

Ejercicios de ampliación
------------------------

- Usando la librería `docopt_cpp`, modifique el fichero `get_pitch.cpp` para incorporar los parámetros del
  detector a los argumentos de la línea de comandos.
  
  Esta técnica le resultará especialmente útil para optimizar los parámetros del detector. Recuerde que
  una parte importante de la evaluación recaerá en el resultado obtenido en la detección de pitch en la
  base de datos.

  * Inserte un *pantallazo* en el que se vea el mensaje de ayuda del programa y un ejemplo de utilización
    con los argumentos añadidos.

- Implemente las técnicas que considere oportunas para optimizar las prestaciones del sistema de detección
  de pitch.

  Entre las posibles mejoras, puede escoger una o más de las siguientes:

  * Técnicas de preprocesado: filtrado paso bajo, *center clipping*, etc.
  * Técnicas de postprocesado: filtro de mediana, *dynamic time warping*, etc.
  * Métodos alternativos a la autocorrelación: procesado cepstral, *average magnitude difference function*
    (AMDF), etc.
  * Optimización **demostrable** de los parámetros que gobiernan el detector, en concreto, de los que
    gobiernan la decisión sonoro/sordo.
  * Cualquier otra técnica que se le pueda ocurrir o encuentre en la literatura.

  Encontrará más información acerca de estas técnicas en las [Transparencias del Curso](https://atenea.upc.edu/pluginfile.php/2908770/mod_resource/content/3/2b_PS%20Techniques.pdf)
  y en [Spoken Language Processing](https://discovery.upc.edu/iii/encore/record/C__Rb1233593?lang=cat).
  También encontrará más información en los anexos del enunciado de esta práctica.

  Incluya, a continuación, una explicación de las técnicas incorporadas al detector. Se valorará la
  inclusión de gráficas, tablas, código o cualquier otra cosa que ayude a comprender el trabajo realizado.

  También se valorará la realización de un estudio de los parámetros involucrados. Por ejemplo, si se opta
  por implementar el filtro de mediana, se valorará el análisis de los resultados obtenidos en función de
  la longitud del filtro.
   

Evaluación *ciega* del detector
-------------------------------

Antes de realizar el *pull request* debe asegurarse de que su repositorio contiene los ficheros necesarios
para compilar los programas correctamente ejecutando `make release`.

Con los ejecutables construidos de esta manera, los profesores de la asignatura procederán a evaluar el
detector con la parte de test de la base de datos (desconocida para los alumnos). Una parte importante de
la nota de la práctica recaerá en el resultado de esta evaluación.
