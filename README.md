# Proyecto de Inteligencia Artificial Universidad de Sevilla



### Por Rafael Balbuena López con DNI 23883362R a 10/02/2021



##### Propuesta Modifica la librería **ID3_C45** para añadir algunas características adicionales:

 Modifica la librería **ID3_C45** para añadir algunas características adicionales:

1. Sustituir el uso de agentes por el de tablas, de forma que el árbol obtenido sea una tabla que contenga toda la información.
2. Añadir un evaluador que valga tanto para ID3 como para C45 (el actual solo funciona con ID3, la versión sin datos numéricos).
3. Añadir la opción de trabajar con muchos árboles calculados.



##### Representación del problema

1. En primer lugar para que funcione debemos eliminar cualquier función que utilice agentes y añadir la extensión que nos permita crear y manejar tablas. 

   Se creará una tabla Inicial,  la cual tendrá como clave que es lo que estamos buscando y como valor otra tabla que tendrá una única entrada, la cual tendrá como clave el nombre del primer nodo al que se dirigirá el algoritmo y como valor otra tabla con las decisiones que puede tomar.

   En esta tabla la clave se identifica como una decisión y como valores otra tabla que tendrá como clave la opción que puede tomar y como valor tendrá el resultado de seguir esa opción.

   

2. Para este apartado crearemos una función auxiliar que a partir de los datos que queremos evaluar y el nombre del nodo de decisión nos devolverá cuál es el siguiente nodo de decisión. Una vez implementado esto solo tendremos que hacer que mientras lo que devuelva esta función esté entre las claves de la tabla que tenía como claves los nombres de los nodos de decisión siga llamando a esta función pero con el nodo de decisión que devolvió la llamada anterior como nodo de decisión en la siguiente llamada. En el momento que no esté, saldrá del bucle y lo que obtendremos será el resultado de la evaluación.

   

3. Para usar varios árboles crearemos una variable global que será una tabla. Estas tablas tendrán como valor el resultado de la función de crear tablas  a partir de árboles y como clave utilizará el nombre que le hemos dado previamente. Para ello crearemos la Variable tableStore y una función que la inicializa. Cuando acabe de crear la tabla simplemente la guardará en la variable global.



##### Implementación

Funciones añadidas a la librería de ID3_C45:

Función sep-aux [rep]: A partir de una cadena que contiene "<=" devuelve una lista con dos objetos, el segundo es el valor numérico que está a la derecha del atributo y al que previamente se le había añadido "<=" y el primero es el nombre del atributo.

Se ha modificado la función ID3:value-of [att dat]: se le ha añadido la condición de que si hay "<=" llame a la función sep-aux [rep] y el atributo que evaluará será el primero de la lista que devuelva.

ID3:evaluate-aux [datum att-target]: Tiene 3 bucles. En el primero busca cuales son las opciones que tiene ese nodo de decisión. El segundo busca en lista de los valores a evaluar cuál es la opción que tiene asignada. El tercero busca cuál de entre las opciones que buscamos en el primer bucle tiene el valor que obtuvimos en el segundo bucles y una vez encontrado, devuelve el elemento de la derecha, es decir, el resultado de escoger esa opción. Ya sea otro nodo de decisión o el resultado de la búsqueda. Si el resultado del segundo bucle es un número, crea una cadena en forma de condición que debe cumplir para que lo devuelva y lo ejecuta con runresult .

ID3:evaluate [datum tree-Name]:obtiene la tabla con el nombre proporcionado y llamará a la función hasta que el resultado no se encuentre en la tabla con el nombre de los nodos de decisión como claves y la devolverá.

ID3:ID3_aux [att-target df]: Si hay error en la tabla devuelve error, en caso contrario,  si el valor objetivo no está clasificado,se hace un bucle y cada opción de una misma decisión se añadirá a una tabla junto con la decisión a la que hace referencia y que posteriormente añadiremos a la tabla junto con la decisión como clave.

ID3:ID3 [att-target df Var-name]: Inicializa las variables globales encapsula las tablas según explicado en la representación del problema y llama a la función para crear la tabla.

ID3:tableList [att-target df Var-name]: Inicializa la variable tableStore para guardar el resultado de las tablas. Hay que inicializarlo desde el código si queremos que se guarden.

extensions [table] : es la extensión que usaremos para manejar las tablas

globals [ : estas son las variables globales
  subDict: la tabla con las decisiones posibles
  tableStore: la tabla con los arboles guardados.
  fnode: la tabla con el primer nodo como key y subDict como valor
]

##### Experimentos

Para los experimentos añadimos una llamada a la función test en la función main-C4.5. Dentro de test estará el                         show ID3:evaluate, vamos a utilizar los datos de PlayGolfNum.data y ponemos los valores necesarios.

show ID3:evaluate [["Outlook" "Sunny"] [ "Temp" 69] ["Humidity" 60] ["Wind" "Weak"] ] "arbol1"

Esto significa que los datos que están introducidos en el ID3:evaluate lo compara con el arbol1 y nos devolverá "yes" ya que es una de las entradas de la tabla.

Si hacemos una llamada para que nos muestre la variable tableStore sería así:

observer: 

{{table: [["arbol1"    																			 ;nombre del árbol

{{table: [["PlayGolf"  																			;objetivo

 {{table: [["Outlook"  																			;primer nodo

{{table: [["Outlook" {{table:  																;decisión Outlook

​												[["Sunny" "Humidity<=70"] 				

​												["Overcast" "Yes"]

​												 ["Rain" "Wind"]]}}]   							

​              ["Humidity<=70" {{table:														;decisión Humidity<=70

​												 [[false "No"]

​												[true "Yes"]]}}]		

​												

​              ["Wind" {{table:																		;decisión Wind

​												 [["Weak" "Yes"] 

​												["Strong" "No"]]

}}]]}}]]}}]]}}; 									  		 

Dentro de las decisiones tendremos las opciones que puede elegir y lo que nos devuelve si escogemos esa decisión Por ejemplo en el caso de Outlook -> Sunny -> "Humidity<=70"



##### Si usamos ID3:C4.5 [att-target Num-atts df Var-name]

att-targert = "PlayGolf"

Num-atts= ["Temp" "Humidity"] 

Var-name = "arbol1"

Obtendrá las variables de la tabla transformará las partes numéricas en cadenas  (df) , las imprimirá por pantalla y llamará a ID3:ID3 como si fuese un ID3:ID3 normal.

ID3:ID3 [att-target df Var-name]

Aquí inicializará las tablas y llamará a  ID3:ID3_aux att-target df 

en mx se guardará la decisión

en u se guardarás las opciones que tiene cada atributo

Posteriormente para cada uno de las opciones de cada decisión se creará una tabla por separado con la clave que es la opción misma y el valor que es el siguiente nodo o decisión al que llegará si escoge esta opción. Posteriormente esta tabla es añadida a otra tabla que contendrá como clave la decisión mx, que es la que estamos usando en este momento. Esto se repite para todos los atributos.

Por último, una vez realizado la función auxiliar se termina de encapsular las tablas para que queden como el resultado anterior. y devolverá la tabla



##### Si usamos  ID3:evaluate [datum tree-Name]

datum = [["Outlook" "Sunny"] [ "Temp" 69] ["Humidity" 60] ["Wind" "Weak"] ]

tree-Name = "arbol1"

Aquí hace la operación inversa de la anterior, lo que hace es desencapsular la tabla anterior y llama a la función:

ID3:evaluate-aux [datum att-target] 

hasta que lo que devuelva no sea una decisión que lo sabremos porque no está en la tabla que tiene como claves las decisiones.

En el primer foreach para cada decisión en la tabla de decisión, que son:

[Outlook {{table: [["Sunny" "Humidity<=70"] ["Overcast" "Yes"] ["Rain" "Wind"]]}}]
[Humidity<=70 {{table: [[false "No"] [true "Yes"]]}}]
[Wind {{table: [["Weak" "Yes"] ["Strong" "No"]]}}]

si att-target, que es una de las decisiones, está en la tabla, devuelve las opciones posibles de la clave, en este caso si es outlook devolvería en ls:

{{table: [["Sunny" "Humidity<=70"] ["Overcast" "Yes"] ["Rain" "Wind"]]}}

En el segundo foreach buscaría en el datum cual es el valor asociado a att-target y lo devolvería en lk:

"Sunny"

En el tercer foreach de cada opción posible de las obtenidas en el primer foreach encontrar cuál es el valor asociado al valor devuelto por el segundo foreach:

"Humidity<=70"

Lo devolvería como valor y como está en la tabla con las decisiones, recordemos que es:

[Outlook {{table: [["Sunny" "Humidity<=70"] ["Overcast" "Yes"] ["Rain" "Wind"]]}}]
[**Humidity<=70** {{table: [[false "No"] [true "Yes"]]}}]
[Wind {{table: [["Weak" "Yes"] ["Strong" "No"]]}}]

volvería a llamar a la función pero ahora como valor objetivo **Humidity<=70** 

ID3:evaluate-aux datum **Humidity<=70** 



##### Conclusión

El uso de tablas es un recurso valioso que nos proporciona Netlogo que nos puede ayudar en muy diversas ocaciones, ya que es más rápido que el uso de agentes o listas y puede ser almacenado con facilidad. 

Sin embargo es mucho menos intuitivo y necesita mucha más documentación para poder entender qué es lo que están haciendo las funciones al contrario que con agentes, que puedes visualizar qué es lo que está haciendo de inmediato. 

Esto nos lleva a escoger la facilidad de implementación en detrimento de la velocidad, ya que no es un aumento tan dramático como para que nos preocupemos por ese factor, sobre todo cuando las tablas son pequeñas y simples que el resultado es inmediato.

El uso de agentes y tablas al mismo tiempo sería un reto que habría que afrontar para ver si se puede tener lo mejor de ambos tipos de implementaciones.  La experimentación es uno de los pilares fundamentales de la informática.

##### **Bonus**

Se ha modificado la librería original y ahora puede hacer una evaluación de C4.5 mediante agentes. Se añadirá al proyecto como ID3_C45_modified.nls y ID3_C45_tablas.nls será el que estaremos haciendo con tablas.

El repositorio inicial se encuentra en http://www.cs.us.es/~fsancho/?p=inteligencia-artificial-2019-20
