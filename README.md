
# Clean Code en Java

## 1. Nombrado

- Utilizar nombres que revelen la intención
- Evitar desinformación
- Usar nombres con diferencias significativas
- Nombres pronunciables
- Nombres que se puedan buscar
- Nombres con concepto
- Contexto significativo
    
    

### 1.1. Nombres que revelen intención

Mal:

```java
int d; //dias
```

Bien:

```java
int dia;
```

Mal:

```java
// get empleados activos
public List<int[]> getElements(){
	List<int[]> list1 = new ArrayList<>();
	for( int[] x: theList )
		if(x[0] == 1)
			list1.add(x);
	return list1;
}
```

Bien:

```java
public List<Empleado> getEmpleadosActivos(){
	List<Empleado> empleadosActivos = new ArrayList<>();
	for(Empleado empleado : empleados)
		if(empleado.isActive())
			empleadosActivos.add(empleado);
	return empleadosActivos;
}
```

### 1.2. Evitar desinformación

Evitar dejar pistas falsas que creen confusión con respecto a los datos que estamos manejando.

Si tenemos una variable `account`, que en 
realidad es una lista entonces nos está creando la confusión de pensar 
que es una sola cuenta cuando en realidad es una lista. O al revés, una 
variables `accounts` cuando en realidad no es una estructura de datos.

### 1.3. Distinciones significativas

No utilizar el mismo nombre para referirse a cosas diferentes en el mismo ámbito, evita usar nombres arbitrarios:

- `Producto`, `ProductoInfo`, `ProductoData`
- `mensaje`, `elMensaje`

Mal:

```java
public static void copyChars(char[] a1, char[] a2){
	for (int i = 0; i < a1.length; i++)
		a2[i] = a1[i];
}
```

Bien:

```java
public static void copyChars(char[] origen, char[] destino){
	for (int i = 0; i < origen.length; i++)
		destino[i] = origen[i];
}
```

### 1.4. Nombres pronunciables

Mal:

```java
class Fch{
	 Date ff;
	 Date fi;
}
```

Bien:

```java
class Fecha{
	Date fechaFin;
	Date fechaInicio;
}
```

### 1.5. Búsqueda de nombres

Mal:

```java
for (int j = 0; j < 34; j++){
	s += (t[j]*4) / 5;
}
```

Bien:

```java
public static final int DIAS_TRABAJO_POR_SEMANA = 5;

int tareasPorDia = 4; 
int sum = 0;

for (int j = 0; j < tareas.length; j++){
	int tareasDia = tareas[j].estimate * tareasPorDia;
	int tareasSemana = tareasDia / DIAS_TRABAJO_POR_SEMANA;
	sum += tareasSemana;
}
```

En el contexto tradicional de los bucles `for` se suelen utilizar las letras `i`, `j`, `k`, `l`.

Pero en otros contextos no es buena idea utilizar 
variables de una sola letra que impliquen un proceso mental extra de 
tener que saber o mapear mentalmente a qué concepto equivale cada cosa.

### 1.5. Una palabra por concepto

Ser consistente a la hora de nombrar, no utilizar distintas palabras para lo mismo de manera arbitraria:

- `get()`, `fetch()`, `retrieve()`, `find()`, `read()`: Si todos hacen lo mismo en diferentes clases, lo mejor es nombrarlos igual en todas las clases.

## 2. Funciones

### 2.1. Funciones pequeñas

**2, 3, 4 líneas de código** por función está bien, cuantas menos líneas mejor.

### 2.2. Bloques e indentación

Indentación es la sangría que aplicamos al código que está contenido en una estructura: `if`, `else`, `switch`, `for`, `while`. 

Cada bloque debería tener **una o dos líneas como mucho**.

### 2.3. Una sola funcionalidad por función

Mal:

```java
public static boolean validarFecha(String fecha) {
        String[] f = fecha.split("/");
        int dia = Integer.parseInt(f[0]);
        int mes = Integer.parseInt(f[1]);
        int año = Integer.parseInt(f[2]);
        // una fecha es válida si el año es >= 0, el mes está entre 1 y 12, y el día está dentro del rango del mes indicado (suponemos que febrero tiene 28 días)
        int[] diasMes = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
        if (año >= 0 && mes >= 1 && mes <= 12 && dia <= diasMes[mes - 1]) {
            return true;
        } else {
            return false;
        }
    }
```

Bien:

```java
public static boolean validarFecha(String fecha) {
        int[] fechaNum = separarFecha(fecha);
        boolean año = validarAño(fechaNum[2]);
        boolean mes = validarMes(fechaNum[1]);
        boolean dia = validarDia(fechaNum[0], fechaNum[1]);

        return (año) && (mes) && (dia);
    }

private static int[] separarFecha(String fecha){
        String[] fechaString = fecha.split("/");
        int[] fechaNum = new int[fechaString.length];
        for (int i = 0; i < fechaString.length; i++) {
            fechaNum[i] =  Integer.parseInt(fechaString[i]);
        }
        return fechaNum;
    }

private static boolean validarDia(int dia, int mes){
        int[] dias = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
        return dia >= 1 && dia <= dias[mes - 1];
    }

private static boolean validarMes(int mes) {
        return mes >= 1 && mes <= 12;
    }

private static boolean validarAño(int año) {
        return año >= 0;
    }
```

### 2.4. No mezclar niveles de abstracción

- Nivel abstracción alto: `getPrecio();`
- Nivel de abstracción intermedio: `String pagePathName = PathParser.render(pagePath);`
- Nivel de abstracción bajo: `.append(”\n”);`

### 2.5. Lectura descendente

<aside>

⚠️ De alto nivel a bajo nivel

</aside>

El código se lee como una narración descendente.

Cada función vaya seguida de las del siguiente nivel de abstracción, de modo que podamos leer el programa descendiendo un nivel de abstracción cada vez.

Es decir, no mezclar en el orden las funciones de alto nivel con las de bajo ni intercalarlas.

### 2.6. Nombres descriptivos

```java
//Mal
addToDate(date, 1);

//Bien
addMesToDate(1, date);
```

### 2.7. Argumentos

- cero (niládicas)
- uno (monádico)
- dos (diádico)
- tres (triádico)
- más de tres (triádico)

El número ideal de argumentos es cero. Los argumentos dificultan el testing.

### 2.8. Evitar flags

```java
// mal
render(false);

// bien
// funcion para true
renderForSuite();

// funcion para false
renderForSingleTest();
```

### 2.9. Verbos y palabras clave

```java
// tratar de combinar verbo/sustantivo
escribir(String nombre)
escribir("Javi")
```

## 3. Organización de clases

Orden de una clase:

1. Variables/Atributos
    1. Constantes (`final` o `String NOMBRE`) públicas/privadas
    2. Variables estáticas (`static`) privadas
    3. Variables privadas de instancia
2. Funciones públicas
3. Funciones privadas

- Respetar el orden de las clases
- Encapsulación: modificadores `private`/`protected`
- Principio SRP: clases pequeñas y de una sola responsabilidad
- Alta cohesión: una clase en la que cada variable es utilizada por
cada método tiene una cohesión máxima. Cuando la cohesión es alta,
significa que los métodos y las variables de la clase son codependientes y forman un todo lógico.
- Buscamos bajo acoplamiento y alta cohesión
- Romper clases grandes en clases más pequeñas
- Romper funciones en funciones más pequeñas
- Organización para el cambio: principio de Open/Closed, separar todo
en clases más pequeñas en vez de una clase grande de manera que los
nuevos se añaden por medio de nuevas clases y la estructura lo permite.

## 4. SOLID

Acrónimo de los 5 principios para escribir código comprensible y mantenible:

- **Principio de responsabilidad única (SRP)**: Cada
interfaz, clase o método que definamos debe tener un objetivo claramente definido. En esencia, lo ideal es que haga una cosa y la haga bien.
Esto lleva a que los métodos y las clases sean más pequeños y que se
puedan probar.
- **Principio de apertura-cierre (OCP)**: El código que
escribimos debería estar idealmente abierto a la extensión pero cerrado a la modificación. Lo que esto significa es que una clase debe ser
escrita de manera que no haya necesidad de modificarla. Sin embargo,
debería permitir cambios a través de la herencia o la composición.
- **Principio de sustitución de Liskov (LSP)**: Este
principio establece que cada subclase o clase derivada debe ser
sustituible por su clase madre o base. Esto ayuda a reducir el
acoplamiento en el código base y, por tanto, a mejorar la reutilización.
- **Principio de segregación de interfaces (ISP)**:
Implementar una interfaz es una forma de proporcionar un comportamiento
específico a nuestra clase. Sin embargo, una clase no debe implementar
métodos que no necesita. Esto nos obliga a definir interfaces más
pequeñas y centradas.
- **Principio de inversión de la dependencia (DIP)**:
Según este principio, las clases sólo deben depender de las
abstracciones y no de sus implementaciones concretas. Esto significa
efectivamente que una clase no debería ser responsable de crear
instancias para sus dependencias. En su lugar, dichas dependencias deben ser inyectadas en la clase.

## 5. Comentarios

### Malos comentarios

- Comentarios confusos
- Comentarios redundantes
- Comentarios obligatorios (javadoc con muchos o excesivos parámetros)
- Javadoc en código que no es público
- Comentarios de seguimiento de cambios con fechas etc: Se usa control de versiones como por ejemplo Git.
- Comentarios ruido: un comentario indicando cuál es el constructor por defecto
- Comentarios autogenerados por el IDE y que no aportan realmente información
- Código comentado
- Comentarios HTML
- Comentarios muy largos
- Información sin contexto o no evidente: por ejemplo un comentario que dice que agregues 200 bytes a algo
- Información no local: añadir información de aspectos que no pueden ser controlados por dicha función.

### Buenos

- Comentarios legales / copyright
- Comentarios informativos pero breves que muestre determinada importancia o agregue información relevante
- Comentarios que expliquen la intencionalidad del código
- Clarificación: aclarar una expresión regular
- Comentarios TO-DO: siempre y cuando se quiten una vez se haya terminado el desarrollo
- Amplificar o destacar la importancia de un proceso
- Javadocs en APIs públicas
- Advertencia de consecuencias: por ejemplo avisar de que la ejecución de un determinado método puede consumir mucho tiempo.

## 6. Formato

### Formato vertical:

- Cuantas menos líneas en cada archivo de código fuente mejor.
- Ideal: menos de 200 líneas. Combinar esta técnica junto con el Principio de una sola responsabilidad.
- Metáfora del periódico: arriba del todo lo de más alto nivel y a medida que descendemos está lo de más bajo nivel.

- Distancia vertical:
    - Si tenemos una función que llama a otra función entonces interesa que estén próximas
    - Los conceptos que sean afine o relacionados deberían estar más
    próximos verticalmente. Ejemplo: Métodos de la interfaz List, los que
    están relacionados están más próximos
    
- Densidad vertical
    - facilitar la respiración entre sentencias
    - se pueden poner seguidas aquellas sentencias que estén relacionadas o conformen un todo mientras que aquellas que no estén tan relacionadas se pueden separar
    

### Formato horizontal:

- La regla principal es no tener que hacer scroll horizontal
- Tamaño ideal de 120 caracteres (IDEs ya lo controlan)
- Apertura horizontal y densidad: poner espacios entre operadores y variables
- En los argumentos de los métodos no es necesario dejar espacios entre los paréntesis
- Respetar la indentación
- IntelliJ IDEA:
    - Formatear el código con: `Ctrl + Shift + L`
    - Se pueden exportar/importar las configuraciones en File > Manage IDE Settings > Export
    

## 7. Manejo de errores

- Usar excepciones en lugar de códigos de error
- Escribir try-catch de entrada a la hora de desarrollar un código que puede ocasionar excepciones.
- checked vs. unchecked: las excepciones comprobadas (checked) dan
lugar a tener que cambiar la signatura en todos los métodos asociados,
es decir, quien ha invocado a la función que lanza la excepción.
Recomendación: unchecked.
- Casuística en la que desde una función de alto nivel/intermedio nos
puede interesar gestionar todas las excepciones lanzadas por funciones
de bajo nivel siempre y cuando estén relacionadas.
    - Si son totalmente independientes y no están relacionadas puede interesar más tratarlas con try catch en la propia función.
    - El trato de las excepciones lanzadas se podría gestionar con AOP.
- Retorno de valores:
    - No devolver `null`, o lanzar una excepción o usar `Optional`.
    - El principal problema de devolver null o trabajar con null es:
        - Ensucia el código teniendo que añadir las típicas comprobaciones de null-checks
        - Es propenso a bugs y errores: `NullPointerException`
- Pasar valores:
    - No pasar `null` como parámetro. Se suele aplicar programación defensiva y lanzar una excepción de tipo `IllegalArgumentException`.
- A nivel de catch: tratar de capturar siempre excepciones concretas en lugar de `Exception`
- CUIDADO: los niveles de error cuando usamos loggers porque podemos
saturar las alertas al equipo de desarrollo si ponemos como error algo
que en realidad sería de tipo INFO o WARNING.
- Dentro de los catch solemos utilizar una herramienta de logging
(ejemplo log4j) para registrar los errores ya sea en archivos o en
sistemas externos como por ejemplo [Elastic Stack](https://www.elastic.co/es/elastic-stack/).
