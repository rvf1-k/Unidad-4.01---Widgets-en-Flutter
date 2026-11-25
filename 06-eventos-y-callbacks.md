# 6. Eventos y callbacks en Flutter

*Fuente: [Unidad 4.01 - Widgets en Flutter - 6. Eventos y callbacks en Flutter](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76814)*

En los capítulos anteriores hemos aprendido a crear widgets personalizados y actualizar el estado de una pantalla. Ahora vamos a trabajar con los **eventos**, que permiten responder a las acciones del usuario.

También veremos cómo comunicar esas acciones entre widgets mediante **callbacks**, sin que los componentes reutilizables tengan que conocer toda la lógica de la aplicación.

### 6.1. ¿Qué es un evento?

Un evento es una acción que ocurre durante el funcionamiento de la aplicación. Por ejemplo, cuando el usuario pulsa un botón, escribe en un campo de texto o selecciona una opción.

Muchos widgets de Flutter tienen propiedades que permiten asociar funciones a estos eventos.

En un botón, una de las más utilizadas es `onPressed`:

```dart
ElevatedButton(
  onPressed: () {
    debugPrint('Botón pulsado');
  },
  child: const Text('Pulsar'),
)
```

Cuando el usuario pulsa el botón, Flutter ejecuta la función indicada en `onPressed`. En este ejemplo, se muestra un mensaje en la consola de depuración.

La expresión `() { ... }` define una **función anónima**, es decir, una función que no necesita tener un nombre propio.

También podemos definir la función por separado:

```dart
void mostrarMensaje() {
  debugPrint('Botón pulsado');
}

// Dentro del método build():
ElevatedButton(
  onPressed: mostrarMensaje,
  child: const Text('Pulsar'),
)
```

En ambos casos, la función se ejecutará cuando se produzca la pulsación.

Fíjate en que escribimos `onPressed: mostrarMensaje`, sin paréntesis. Estamos pasando la función para que Flutter pueda ejecutarla posteriormente.

### 6.2. ¿Qué es un callback?

Un **callback** es una función que pasamos a otro componente para que pueda ejecutarla cuando ocurra una determinada acción.

Imagina que creamos un botón personalizado. Queremos reutilizarlo en varias pantallas, pero no siempre debe realizar la misma acción.

Por ejemplo:

- En una pantalla, el botón mostrará un mensaje.
- En otra, podría abrir una sección de la aplicación.
- En un formulario, podría iniciar la validación de los datos.

La solución consiste en permitir que el widget reciba una función desde la pantalla que lo utiliza.

Flutter proporciona el tipo `VoidCallback` para representar una función que no recibe parámetros y no devuelve ningún valor.

Podemos declarar una propiedad de este tipo:

```
final VoidCallback onPressed;
```

Después, el componente puede utilizar esa propiedad para responder al evento del botón:

```dart
ElevatedButton(
  onPressed: onPressed,
  child: const Text('Ejecutar acción'),
)
```

De esta manera, el botón sabe **cuándo** debe ejecutar una acción, pero la pantalla que lo utiliza decide **qué acción** realizará.

### 6.3. Crear un botón reutilizable con callback

Vamos a crear un widget llamado `BotonAccion`. Recibirá un texto, un icono y una función que se ejecutará cuando el usuario lo pulse.

Dentro de la carpeta `lib/widgets`, crea el archivo `boton_accion.dart` con este contenido:

```dart
import 'package:flutter/material.dart';

class BotonAccion extends StatelessWidget {
  final String texto;
  final IconData icono;
  final VoidCallback onPressed;

  const BotonAccion({
    super.key,
    required this.texto,
    required this.onPressed,
    this.icono = Icons.notifications_outlined,
  });

  @override
  Widget build(BuildContext context) {
    return ElevatedButton.icon(
      onPressed: onPressed,
      icon: Icon(icono),
      label: Text(texto),
    );
  }
}
```

Observa las propiedades que hemos definido:

- `texto`: contiene la etiqueta que aparecerá en el botón.
- `icono`: permite elegir un icono. Si no indicamos ninguno, se utiliza el valor por defecto.
- `onPressed`: recibe la función que debe ejecutarse al pulsarlo.

El componente no contiene ninguna acción concreta. Simplemente recibe un callback y lo asigna al evento del botón.

Por ejemplo, podríamos utilizarlo así desde una pantalla:

```
BotonAccion(
  texto: 'Consultar',
  icono: Icons.info_outline,
  onPressed: () {
    debugPrint('Consultar información');
  },
)
```

Si creamos otra instancia, podemos asignarle una función distinta sin modificar `BotonAccion`.

### 6.4. Mostrar mensajes con SnackBar

Hasta ahora hemos utilizado `debugPrint()` para comprobar que una función se ejecuta. Sin embargo, sus mensajes aparecen en la consola, no en la interfaz que ve el usuario.

Para mostrar una notificación breve dentro de la aplicación podemos utilizar `SnackBar`.

Un `SnackBar` es un mensaje temporal que aparece sobre la interfaz, normalmente en la parte inferior de la pantalla.

Para mostrarlo utilizamos `ScaffoldMessenger`:

```dart
ScaffoldMessenger.of(context).showSnackBar(
  const SnackBar(
    content: Text('Operación completada'),
  ),
);
```

En este código:

- `ScaffoldMessenger.of(context)` localiza el gestor de mensajes disponible en ese contexto de la aplicación.
- `showSnackBar()` solicita que se muestre el mensaje.
- `SnackBar` define el mensaje temporal.
- `content` contiene el widget que mostrará su contenido.

Para utilizar este ejemplo necesitamos un contexto adecuado, normalmente dentro de una aplicación `MaterialApp` con una pantalla `Scaffold`.

También podemos configurar la duración del mensaje:

```dart
ScaffoldMessenger.of(context).showSnackBar(
  const SnackBar(
    content: Text('Datos guardados'),
    duration: Duration(seconds: 3),
  ),
);
```

Si queremos sustituir un mensaje que ya se está mostrando, podemos llamar antes a `hideCurrentSnackBar()`.

### 6.5. Ejemplo completo: botones con diferentes acciones

Vamos a utilizar el componente `BotonAccion` para crear una pantalla con dos botones. Cada uno mostrará un mensaje diferente al pulsarlo.

La estructura del proyecto será la siguiente:

```
lib/
├── main.dart
└── widgets/
    └── boton_accion.dart
```

Mantén en `lib/widgets/boton_accion.dart` el código completo del apartado 6.3.

Después, sustituye el contenido de `lib/main.dart` por este ejemplo:

```dart
import 'package:flutter/material.dart';
import 'widgets/boton_accion.dart';

void main() {
  runApp(const EjemploEventosApp());
}

class EjemploEventosApp extends StatelessWidget {
  const EjemploEventosApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: PantallaEventos(),
    );
  }
}

class PantallaEventos extends StatelessWidget {
  const PantallaEventos({super.key});

  void _mostrarMensaje(
    BuildContext context,
    String mensaje,
  ) {
    final messenger = ScaffoldMessenger.of(context);

    messenger.hideCurrentSnackBar();

    messenger.showSnackBar(
      SnackBar(
        content: Text(mensaje),
        duration: const Duration(seconds: 3),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Eventos y callbacks'),
      ),
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            const Text(
              'Selecciona una acción',
              style: TextStyle(fontSize: 20),
            ),
            const SizedBox(height: 20),
            BotonAccion(
              texto: 'Ver información',
              icono: Icons.info_outline,
              onPressed: () {
                _mostrarMensaje(
                  context,
                  'Has consultado la información.',
                );
              },
            ),
            const SizedBox(height: 12),
            BotonAccion(
              texto: 'Confirmar',
              icono: Icons.check_circle_outline,
              onPressed: () {
                _mostrarMensaje(
                  context,
                  'Acción confirmada correctamente.',
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

Ejecuta la aplicación con `flutter run -d chrome`, si Chrome está disponible como dispositivo.

Comprueba que cada botón muestra el mensaje que le corresponde.

En este ejemplo hemos separado las responsabilidades:

- `BotonAccion` define el aspecto del componente y recibe un callback.
- `PantallaEventos` decide qué debe ocurrir cuando se pulsa cada botón.
- `_mostrarMensaje()` contiene la lógica común para mostrar los mensajes.

La comunicación funciona de la siguiente manera:

1. La pantalla crea una instancia de `BotonAccion` y le proporciona una función mediante `onPressed`.
2. El usuario pulsa el botón.
3. El componente ejecuta la función que ha recibido.
4. La pantalla muestra el mensaje correspondiente mediante `ScaffoldMessenger`.

Este mecanismo permite reutilizar componentes sin introducir dentro de ellos todas las acciones de la aplicación.

### 6.6. Errores habituales con los callbacks

Al empezar a trabajar con eventos es frecuente confundir la referencia a una función con su ejecución.

Por ejemplo, si una función no recibe parámetros:

```
// Correcto: pasamos la función.
onPressed: mostrarMensaje

// Incorrecto: intentamos ejecutar la función
// durante la construcción del widget.
onPressed: mostrarMensaje()
```

En el segundo caso, si `mostrarMensaje()` devuelve `void`, Dart producirá un error de tipos porque `onPressed` espera una función.

Si necesitamos pasar argumentos, podemos utilizar una función anónima:

```
onPressed: () {
  mostrarMensaje('Hola');
}
```

Otro aspecto importante es que `VoidCallback` representa una función sin parámetros. Si un componente necesita comunicar información al widget padre, también podemos utilizar otros tipos de callback, como `ValueChanged`, que estudiaremos cuando lo necesitemos.

**Documentación de consulta:** [Tipo VoidCallback](https://api.flutter.dev/flutter/foundation/VoidCallback.html), [método showSnackBar()](https://api.flutter.dev/flutter/material/ScaffoldMessengerState/showSnackBar.html) y [interactividad en Flutter](https://docs.flutter.dev/ui/interactivity).
