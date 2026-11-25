# 9. Depuración y comprobación del funcionamiento

*Fuente: [Unidad 4.01 - Widgets en Flutter - 9. Depuración y comprobación del funcionamiento](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76817)*

Cuando desarrollamos una aplicación, es habitual encontrar errores: un cálculo que devuelve un resultado incorrecto, un botón que no responde o un widget que no se muestra como esperábamos.

**Depurar** consiste en localizar la causa de estos problemas y corregirla. Flutter y Visual Studio Code proporcionan herramientas para examinar el código mientras se ejecuta la aplicación, comprobar valores y entender qué está ocurriendo.

### 9.1. Identificar los tipos de errores

No todos los errores tienen el mismo origen. Podemos distinguir tres situaciones habituales:

- **Errores de código:** una instrucción incorrecta, un tipo incompatible o una variable que no existe. Normalmente los detecta el editor o el analizador de Dart.
- **Errores de ejecución:** aparecen mientras la aplicación funciona, por ejemplo cuando una conversión de datos provoca una excepción.
- **Errores de lógica:** la aplicación se ejecuta, pero el resultado no es el esperado. Por ejemplo, una calculadora que utiliza una fórmula incorrecta.

Los errores de lógica pueden resultar especialmente difíciles de detectar porque la aplicación puede funcionar aparentemente bien.

Antes de modificar el código, intenta responder a tres preguntas: qué debería ocurrir, qué ocurre realmente y en qué momento aparece la diferencia.

### 9.2. Consultar los mensajes de la consola

Una de las formas más sencillas de investigar el funcionamiento de una aplicación es mostrar información en la consola de depuración.

Flutter proporciona la función `debugPrint()`, que permite escribir mensajes durante la ejecución.

```
debugPrint('Se ha pulsado el botón');
```

También podemos incluir el valor de una variable:

```
final temperatura = 20.0;

debugPrint('Temperatura actual: $temperatura');
```

El mensaje permite comprobar qué valor tiene la variable cuando se ejecuta esa instrucción.

Vamos a utilizarlo en el conversor de temperatura del capítulo anterior.

Localiza el `Slider` de `PantallaConversor` y modifica su callback `onChanged` de esta manera:

```dart
onChanged: (valor) {
  debugPrint('Nuevo valor del Slider: $valor');

  setState(() {
    _celsius = valor;
  });
},
```

Ejecuta la aplicación y desplaza el control. Observa los mensajes que aparecen en la terminal o en la consola de depuración.

El callback se ejecuta cada vez que el control comunica un nuevo valor. Por eso, mientras lo desplazas, pueden aparecer numerosos mensajes.

`debugPrint()` es útil para investigar problemas sencillos, pero no conviene llenar el código de mensajes innecesarios. Cuando termines de depurar, elimina los que ya no aporten información.

### 9.3. Utilizar puntos de interrupción

Los mensajes de consola permiten observar valores, pero a veces necesitamos detener la ejecución y examinar el código con más detalle.

Un **punto de interrupción** o *breakpoint* indica al depurador dónde debe detenerse cuando la ejecución alcanza una determinada línea.

Vamos a probarlo con nuestro conversor de temperatura:

1. Abre el proyecto en Visual Studio Code.
2. Localiza la línea `_celsius = valor;` dentro del callback del `Slider`.
3. Haz clic en el margen izquierdo, junto al número de línea. Aparecerá un punto rojo que indica el breakpoint.
4. Selecciona Chrome como dispositivo, si está disponible, e inicia la depuración con `F5`.
5. Cuando la aplicación se abra, mueve el control deslizante.

La ejecución debería detenerse al alcanzar el punto de interrupción. En ese momento podrás consultar el valor de `valor` y el valor que todavía tiene `_celsius`.

En el panel de depuración de Visual Studio Code encontrarás herramientas para:

- **Inspeccionar variables:** consultar los datos disponibles en el punto donde se ha detenido la ejecución.
- **Avanzar paso a paso:** ejecutar las siguientes instrucciones y observar sus efectos.
- **Continuar:** reanudar el funcionamiento normal de la aplicación hasta encontrar otro breakpoint.

Este procedimiento permite comprobar en qué momento cambia una variable y qué instrucciones se ejecutan antes de obtener un resultado.

### 9.4. Ejemplo práctico: localizar un error de cálculo

Vamos a introducir un error de forma intencionada en el conversor para aprender a detectarlo.

Abre `lib/utils/temperatura.dart` y modifica temporalmente la función:

```
// Versión con un error intencionado.

double convertirAFahrenheit(double celsius) {
  return celsius * 9 / 5;
}
```

Ejecuta la aplicación y selecciona 20 grados Celsius.

La aplicación se ejecuta, pero muestra un resultado incorrecto: 36 grados Fahrenheit en lugar de 68.

El problema está en que hemos olvidado sumar 32 al resultado de la operación.

Para localizarlo, puedes añadir un mensaje de depuración:

```
double convertirAFahrenheit(double celsius) {
  final resultado = celsius * 9 / 5;

  debugPrint(
    'Entrada: $celsius, resultado: $resultado',
  );

  return resultado;
}
```

En este caso, para utilizar `debugPrint()` dentro de `temperatura.dart`, debes añadir al principio del archivo:

```dart
import 'package:flutter/foundation.dart';
```

También puedes colocar un punto de interrupción dentro de la función y examinar sus variables sin añadir mensajes de consola.

Una vez identificado el problema, recupera la versión correcta de `temperatura.dart`:

```
double convertirAFahrenheit(double celsius) {
  return celsius * 9 / 5 + 32;
}
```

Comprueba que 0 grados Celsius equivalen a 32 Fahrenheit y que 20 grados Celsius equivalen a 68 Fahrenheit.

Este ejemplo demuestra que **una aplicación puede ejecutarse sin errores de código y, aun así, producir resultados incorrectos**. Por eso debemos comprobar su comportamiento con valores conocidos.

### 9.5. Analizar el proyecto con flutter analyze

Flutter incluye herramientas para detectar problemas en el código antes de ejecutar la aplicación.

Desde la carpeta raíz del proyecto, puedes utilizar:

```
flutter analyze
```

Este comando realiza un análisis estático del proyecto y puede detectar errores, advertencias y recomendaciones relacionadas con el código Dart.

Por ejemplo, puede avisarte de:

- Variables o métodos que no existen.
- Incompatibilidades entre tipos de datos.
- Importaciones que no se utilizan.
- Problemas detectados por las reglas de análisis configuradas en el proyecto.

Sin embargo, `flutter analyze` no puede determinar si nuestra fórmula de conversión es matemáticamente correcta. Para eso necesitamos comprobar los resultados esperados.

También puedes consultar los problemas detectados directamente desde el panel **Problems** de Visual Studio Code.

### 9.6. Investigar problemas visuales con Flutter Inspector

Algunos errores no afectan a los cálculos, sino a la distribución de los elementos en la pantalla.

Por ejemplo, una `Row` puede intentar mostrar más contenido del que cabe horizontalmente, provocando un desbordamiento.

El **Flutter Inspector**, disponible dentro de Flutter DevTools, permite explorar el árbol de widgets y consultar propiedades relacionadas con su distribución.

Con esta herramienta puedes:

- Examinar la estructura de widgets de una pantalla.
- Seleccionar un componente y consultar sus propiedades.
- Investigar problemas de tamaño, alineación y restricciones.

Si, por ejemplo, un texto demasiado largo provoca un desbordamiento en una `Row`, puedes investigar si necesita un `Expanded` para adaptarse al ancho disponible.

El Inspector resulta especialmente útil cuando la pantalla tiene varios widgets anidados y cuesta identificar cuál provoca el problema.

### 9.7. Trabajar sobre un proyecto que no has escrito

Muchas veces no empezarás desde cero: recibirás una aplicación que ya funciona y tendrás que ampliarla o corregirla. Antes de cambiar nada, conviene entender cómo está organizada y comprobar en qué estado la recibes.

#### Paso 1. Abrir y ejecutar el proyecto sin tocarlo

Abre en Visual Studio Code la carpeta que contiene `pubspec.yaml` y descarga sus dependencias:

```
flutter pub get
```

Después ejecútalo y prueba sus pantallas. Lo que veas ahora será tu punto de comparación: si algo deja de funcionar más tarde, sabrás que lo ha provocado tu cambio.

#### Paso 2. Comprobar su estado inicial

Ejecuta el análisis y, si el proyecto incluye la carpeta `test/`, también sus pruebas:

```
flutter analyze
flutter test
```

Anota si aparecen avisos o pruebas que fallan antes de empezar. Así no confundirás un problema que ya existía con uno que hayas introducido tú.

#### Paso 3. Seguir el recorrido desde main.dart

Abre `lib/main.dart` y localiza la propiedad `home` de `MaterialApp`: indica qué pantalla aparece al iniciar. A partir de ahí, sigue los `import` de cada archivo para saber dónde están sus pantallas, sus componentes y sus funciones auxiliares.

Estas herramientas de Visual Studio Code te ahorrarán tiempo:

- **F12** sobre el nombre de una clase o función abre el archivo donde está definida.
- **Ctrl + Shift + F** busca un texto en todos los archivos del proyecto. Es la forma más rápida de encontrar el código que genera un texto visible en pantalla o un comentario concreto.
- **Ctrl + P** abre un archivo escribiendo parte de su nombre.

#### Paso 4. Cambiar poco y comprobar cada cambio

Haz una modificación cada vez y comprueba su resultado con Hot Reload antes de pasar a la siguiente. Si algo falla después de varios cambios seguidos, será mucho más difícil saber cuál lo ha provocado.

Cuando termines, repite `flutter analyze` y, si las hay, `flutter test`. El resultado debe ser igual o mejor que el que anotaste en el paso 2.

**Comprueba lo aprendido:** abre el formulario del capítulo 7 como si no lo hubieras escrito tú. Busca con Ctrl + Shift + F el texto *Introduce una edad válida*, cambia el mensaje por otro más concreto y comprueba que aparece al escribir letras en la edad.

**Documentación de consulta:** [Depuración de aplicaciones Flutter](https://docs.flutter.dev/testing/debugging), [Flutter en Visual Studio Code](https://docs.flutter.dev/tools/vs-code), [Flutter Inspector](https://docs.flutter.dev/tools/devtools/inspector).
