# 8. Separación de la lógica y la interfaz en Flutter

*Fuente: [Unidad 4.01 - Widgets en Flutter - 8. Separación de la lógica y la interfaz en Flutter](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76816)*

Hasta ahora hemos construido interfaces que muestran información, reciben datos y responden a eventos. Sin embargo, algunas aplicaciones también necesitan realizar cálculos, transformar información o aplicar determinadas reglas.

Aunque podemos escribir estas operaciones directamente dentro de los widgets, es recomendable **separar la lógica de la interfaz** para organizar mejor el código y facilitar su comprobación.

### 8.1. Diferenciar la interfaz de la lógica

Imagina una aplicación que convierte temperaturas de grados Celsius a Fahrenheit.

Podemos distinguir dos responsabilidades:

- **Interfaz:** permite seleccionar una temperatura y muestra el resultado de la conversión.
- **Lógica:** recibe una temperatura, realiza el cálculo y devuelve el resultado.

La fórmula para convertir grados Celsius a Fahrenheit es:

```
F = (C × 9 / 5) + 32
```

Por ejemplo, 20 grados Celsius equivalen a 68 grados Fahrenheit.

Este cálculo no necesita conocer los botones, los textos ni los demás widgets de la pantalla. Por tanto, podemos implementarlo mediante una función Dart independiente.

### 8.2. Crear una función independiente

Vamos a crear un archivo para almacenar la función de conversión.

Dentro de `lib`, crea una carpeta llamada `utils` y, en su interior, el archivo `temperatura.dart`.

La estructura del proyecto será:

```
lib/
├── main.dart
└── utils/
    └── temperatura.dart
```

Escribe el siguiente código en `lib/utils/temperatura.dart`:

```
double convertirAFahrenheit(double celsius) {
  return celsius * 9 / 5 + 32;
}
```

La función recibe un número de tipo `double`, aplica la fórmula y devuelve el resultado.

Por ejemplo:

```
final resultado = convertirAFahrenheit(20);
// resultado vale 68.0
```

Fíjate en que esta función:

- No utiliza ningún widget de Flutter.
- No necesita acceder al estado de una pantalla.
- Siempre devuelve el mismo resultado si recibe el mismo valor.

Una función con estas características resulta sencilla de reutilizar y comprobar. Podemos ejecutarla con diferentes valores sin necesidad de abrir la interfaz de la aplicación.

### 8.3. Utilizar la función desde una pantalla

Para utilizar una función definida en otro archivo, debemos importarlo mediante `import`.

En este caso, desde `main.dart` utilizaremos:

```dart
import 'utils/temperatura.dart';
```

Después podremos llamar a `convertirAFahrenheit()` desde cualquier parte de ese archivo.

El funcionamiento será el siguiente:

1. El usuario selecciona una temperatura en la interfaz.
2. La pantalla envía ese valor a `convertirAFahrenheit()`.
3. La función realiza el cálculo y devuelve el resultado.
4. La pantalla muestra el valor convertido.

La interfaz se encarga de la interacción con el usuario y la función independiente se ocupa del cálculo.

### 8.4. Ejemplo completo: conversor de temperatura

Vamos a construir una aplicación que permita seleccionar una temperatura mediante un control deslizante y mostrar su equivalencia en grados Fahrenheit.

Conserva la función del apartado 8.2 en `lib/utils/temperatura.dart`.

Después, sustituye el contenido de `lib/main.dart` por el siguiente código:

```dart
import 'package:flutter/material.dart';
import 'utils/temperatura.dart';

void main() {
  runApp(const ConversorApp());
}

class ConversorApp extends StatelessWidget {
  const ConversorApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: PantallaConversor(),
    );
  }
}

class PantallaConversor extends StatefulWidget {
  const PantallaConversor({super.key});

  @override
  State<PantallaConversor> createState() =>
      _PantallaConversorState();
}

class _PantallaConversorState
    extends State<PantallaConversor> {

  double _celsius = 20;

  @override
  Widget build(BuildContext context) {
    final fahrenheit = convertirAFahrenheit(_celsius);

    return Scaffold(
      appBar: AppBar(
        title: const Text('Conversor de temperatura'),
      ),
      body: Center(
        child: Padding(
          padding: const EdgeInsets.all(24),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              const Text(
                'Selecciona una temperatura',
                style: TextStyle(fontSize: 18),
              ),

              const SizedBox(height: 20),

              Text(
                '${_celsius.toStringAsFixed(0)} °C',
                style: const TextStyle(
                  fontSize: 28,
                  fontWeight: FontWeight.bold,
                ),
              ),

              Slider(
                value: _celsius,
                min: -20,
                max: 50,
                divisions: 70,
                label: '${_celsius.round()} °C',
                onChanged: (valor) {
                  setState(() {
                    _celsius = valor;
                  });
                },
              ),

              const SizedBox(height: 24),

              const Text(
                'Equivalencia en Fahrenheit',
                style: TextStyle(fontSize: 16),
              ),

              const SizedBox(height: 8),

              Text(
                '${fahrenheit.toStringAsFixed(1)} °F',
                style: const TextStyle(
                  fontSize: 32,
                  fontWeight: FontWeight.bold,
                  color: Colors.blue,
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

Ejecuta la aplicación con `flutter run -d chrome`, si Chrome está disponible como dispositivo.

Desplaza el control y observa cómo cambia la temperatura seleccionada y el resultado de la conversión.

El ejemplo incorpora un widget nuevo: `Slider`, que permite seleccionar un valor dentro de un intervalo.

Sus propiedades principales son:

- `value`: indica el valor seleccionado actualmente.
- `min` y `max`: establecen los límites del intervalo.
- `divisions`: divide el intervalo en pasos.
- `onChanged`: recibe el nuevo valor cuando el usuario mueve el control.

Cuando cambia el valor del `Slider`, utilizamos `setState()` para actualizar la temperatura almacenada en `_celsius`.

Flutter vuelve a ejecutar `build()` y calculamos de nuevo el resultado mediante:

```
final fahrenheit = convertirAFahrenheit(_celsius);
```

Observa que la fórmula de conversión no aparece en el widget. La pantalla utiliza la función que hemos definido en otro archivo.

También utilizamos `toStringAsFixed(1)` para mostrar el resultado con un decimal. Este método modifica su representación como texto, pero no cambia el valor numérico que devuelve la función.

### 8.5. ¿Por qué esta separación facilita las pruebas?

Una ventaja de mantener los cálculos fuera de los widgets es que podemos comprobar su funcionamiento de manera independiente.

Para verificar `convertirAFahrenheit()`, podemos utilizar diferentes entradas y comparar los resultados con los valores esperados:

- 0 °C debe devolver 32 °F.
- 20 °C debe devolver 68 °F.
- 30 °C debe devolver 86 °F.

Para comprobar estos cálculos no necesitamos simular una pulsación ni mostrar una pantalla. Basta con ejecutar la función y comparar su resultado.

Esto es precisamente lo que haremos mediante las **pruebas unitarias** en el siguiente libro de la unidad.

También podremos utilizar **pruebas de widget** para comprobar que la interfaz responde correctamente a las acciones del usuario.

La diferencia es importante: una prueba unitaria puede comprobar que la fórmula devuelve el resultado correcto, mientras que una prueba de widget puede comprobar que la pantalla muestra ese resultado después de una interacción.

**Documentación de consulta:** [Arquitectura de aplicaciones Flutter](https://docs.flutter.dev/app-architecture/guide), [bibliotecas e importaciones en Dart](https://dart.dev/language/libraries) y [referencia del widget Slider](https://api.flutter.dev/flutter/material/Slider-class.html).
