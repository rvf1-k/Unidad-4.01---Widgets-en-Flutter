# 7. Formularios y entrada de datos

*Fuente: [Unidad 4.01 - Widgets en Flutter - 7. Formularios y entrada de datos](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76815)*

Hasta ahora hemos creado interfaces con botones, textos y componentes reutilizables. Sin embargo, muchas aplicaciones necesitan que el usuario introduzca información: su nombre, una cantidad, una dirección de correo o los datos necesarios para realizar un cálculo.

Flutter proporciona widgets para recoger esta información, acceder a los valores introducidos y comprobar que sean correctos antes de utilizarlos.

### 7.1. Introducir texto con TextField

Ya utilizaste `TextField` en el apartado 3.5. Recuerda que `InputDecoration` configura su apariencia con propiedades como `labelText`, `hintText`, `prefixIcon` y `border`. En este capítulo nos centramos en recoger y comprobar lo que escribe el usuario.

Cuando el campo debe recibir un número, conviene indicar el tipo de teclado:

```
const TextField(
  keyboardType: TextInputType.number,
  decoration: InputDecoration(
    labelText: 'Cantidad',
    border: OutlineInputBorder(),
  ),
)
```

`TextInputType.number` solicita un teclado adecuado para introducir números, especialmente en dispositivos móviles. Sin embargo, **no garantiza que el usuario escriba un número válido**. Tendremos que comprobar los datos antes de utilizarlos.

Si la cantidad puede llevar decimales, utiliza `TextInputType.numberWithOptions(decimal: true)`. Así el teclado del móvil incluye también el separador decimal.

### 7.2. Obtener los valores introducidos

Mostrar un campo de texto es sencillo, pero normalmente necesitaremos recuperar su contenido para realizar alguna operación.

Flutter ofrece dos formas habituales de hacerlo: utilizar `onChanged` o trabajar con un `TextEditingController`.

#### Utilizar onChanged

La propiedad `onChanged` permite ejecutar una función cada vez que el usuario modifica el contenido del campo.

```
TextField(
  onChanged: (valor) {
    debugPrint('Texto actual: $valor');
  },
)
```

En este ejemplo, `valor` contiene el texto actualizado. El callback se ejecuta mientras el usuario escribe.

Este mecanismo resulta útil cuando queremos reaccionar inmediatamente a los cambios, por ejemplo para filtrar una lista de resultados.

#### Utilizar TextEditingController

Un `TextEditingController` permite acceder al texto de un campo y modificarlo desde el código de la aplicación.

Primero declaramos el controlador dentro del objeto `State` de un widget:

```
final _nombreController = TextEditingController();
```

Después lo asociamos al campo mediante la propiedad `controller`:

```
TextField(
  controller: _nombreController,
  decoration: const InputDecoration(
    labelText: 'Nombre',
  ),
)
```

Podemos recuperar el contenido cuando lo necesitemos:

```
final nombre = _nombreController.text.trim();
```

La propiedad `text` devuelve el contenido del campo como una cadena de texto. El método `trim()` elimina los espacios sobrantes al principio y al final.

Cuando creamos un controlador, también debemos liberarlo cuando deje de utilizarse. Para ello, sobrescribimos `dispose()` dentro de la clase `State`:

```
@override
void dispose() {
  _nombreController.dispose();
  super.dispose();
}
```

Recuerda que `dispose()` forma parte del ciclo de vida de los widgets con estado, que aprendimos en el capítulo 5.

### 7.3. Validar datos con Form y TextFormField

En una aplicación real no basta con recoger datos. También debemos comprobar que cumplen las condiciones necesarias antes de procesarlos.

Por ejemplo, podemos necesitar que un campo no esté vacío o que una cantidad sea mayor que cero.

Para trabajar con estas comprobaciones, Flutter proporciona dos widgets:

- **Form:** agrupa los campos de un formulario y permite validarlos conjuntamente.
- **TextFormField:** funciona como un campo de texto, pero incorpora herramientas de validación.

La propiedad más importante para empezar es `validator`, que recibe una función encargada de comprobar el valor introducido.

Por ejemplo:

```dart
TextFormField(
  decoration: const InputDecoration(
    labelText: 'Nombre',
    border: OutlineInputBorder(),
  ),
  validator: (valor) {
    if (valor == null || valor.trim().isEmpty) {
      return 'Introduce tu nombre';
    }
    return null;
  },
)
```

El funcionamiento es sencillo:

- Si el dato es incorrecto, `validator` devuelve un texto con el error que debe mostrarse.
- Si el dato es válido, devuelve `null`.

Para comprobar todos los campos podemos asociar una clave al formulario:

```
final _formKey = GlobalKey<FormState>();
```

La clave permite acceder al estado del formulario y ejecutar su método `validate()`:

```
if (_formKey.currentState!.validate()) {
  // Todos los campos han superado la validación.
}
```

Cuando llamamos a `validate()`, Flutter ejecuta las funciones de validación de los campos del formulario. El método devuelve `true` si todos los datos son válidos y `false` si alguno presenta errores.

### 7.4. Ejemplo completo: formulario de registro

Vamos a construir un formulario sencillo que solicite el nombre y la edad de una persona.

El comportamiento será el siguiente:

1. El usuario introduce su nombre y su edad.
2. Al pulsar el botón, la aplicación comprueba que el nombre no esté vacío y que la edad sea un número entero positivo.
3. Si los datos son incorrectos, aparecen mensajes junto a los campos.
4. Si son correctos, se muestra un `SnackBar` con la información introducida.

Abre `lib/main.dart` en un proyecto de prueba y sustituye su contenido por el siguiente código:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const EjemploFormularioApp());
}

class EjemploFormularioApp extends StatelessWidget {
  const EjemploFormularioApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: PantallaFormulario(),
    );
  }
}

class PantallaFormulario extends StatefulWidget {
  const PantallaFormulario({super.key});

  @override
  State<PantallaFormulario> createState() =>
      _PantallaFormularioState();
}

class _PantallaFormularioState
    extends State<PantallaFormulario> {

  final _formKey = GlobalKey<FormState>();

  final _nombreController = TextEditingController();
  final _edadController = TextEditingController();

  @override
  void dispose() {
    _nombreController.dispose();
    _edadController.dispose();
    super.dispose();
  }

  void _guardar() {
    if (!_formKey.currentState!.validate()) {
      return;
    }

    final nombre = _nombreController.text.trim();
    final edad = int.parse(_edadController.text.trim());

    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(
          'Ficha creada: $nombre, $edad años.',
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Formulario de registro'),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(20),
        child: Form(
          key: _formKey,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              const Text(
                'Datos personales',
                style: TextStyle(
                  fontSize: 24,
                  fontWeight: FontWeight.bold,
                ),
              ),

              const SizedBox(height: 24),

              TextFormField(
                key: const Key('campoNombre'),
                controller: _nombreController,
                decoration: const InputDecoration(
                  labelText: 'Nombre',
                  border: OutlineInputBorder(),
                ),
                validator: (valor) {
                  if (valor == null ||
                      valor.trim().isEmpty) {
                    return 'Introduce tu nombre';
                  }
                  return null;
                },
              ),

              const SizedBox(height: 16),

              TextFormField(
                key: const Key('campoEdad'),
                controller: _edadController,
                keyboardType: TextInputType.number,
                decoration: const InputDecoration(
                  labelText: 'Edad',
                  border: OutlineInputBorder(),
                ),
                validator: (valor) {
                  final edad =
                      int.tryParse(valor?.trim() ?? '');

                  if (edad == null || edad <= 0) {
                    return 'Introduce una edad válida';
                  }
                  return null;
                },
              ),

              const SizedBox(height: 24),

              ElevatedButton(
                onPressed: _guardar,
                child: const Text('Guardar'),
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

Prueba el formulario en diferentes situaciones: dejando los campos vacíos, escribiendo letras en la edad e introduciendo finalmente datos válidos.

Observa algunos elementos importantes del ejemplo:

- `PantallaFormulario` es un `StatefulWidget` porque gestiona los controladores y el estado del formulario.
- `_formKey` permite ejecutar la validación conjunta.
- `TextEditingController` permite recuperar los valores introducidos.
- `key: const Key('campoNombre')` y `key: const Key('campoEdad')` dan un nombre único a cada campo. No cambian nada en pantalla, pero permiten localizarlos sin confusión. Las utilizaremos en las pruebas de widget del siguiente libro.
- `int.tryParse()` intenta convertir una cadena en un número entero. Si no puede hacerlo, devuelve `null`.
- `_guardar()` solo continúa cuando todos los campos superan la validación.
- `SingleChildScrollView` permite desplazar el contenido si no cabe en la pantalla, por ejemplo cuando aparece el teclado de un móvil.

En `_guardar()` utilizamos `int.parse()` después de validar el formulario, porque ya hemos comprobado que la edad contiene un entero válido.

El ejemplo no almacena los datos de forma permanente. El botón muestra una confirmación mediante `SnackBar`. Guardar información en una base de datos o en el almacenamiento del dispositivo requeriría incorporar otras funcionalidades.

### 7.5. Errores habituales al trabajar con formularios

Al crear formularios conviene tener presentes algunos errores frecuentes:

- **Suponer que un campo numérico solo admite números.** El tipo de teclado es una ayuda, pero no sustituye la validación.
- **Convertir texto sin comprobarlo.** `int.parse()` puede lanzar una excepción si recibe un valor incorrecto. `int.tryParse()` permite comprobar la conversión de forma controlada.
- **Usar un entero cuando el dato lleva decimales.** Para valores como una altura o un precio, utiliza `double.tryParse()`. Ten en cuenta que Dart espera el punto como separador: `double.tryParse('1,75')` devuelve `null`. Si quieres aceptar también la coma, sustitúyela antes de convertir: `double.tryParse(texto.trim().replaceAll(',', '.'))`.
- **Olvidar la validación.** Aunque el formulario tenga funciones `validator`, debemos ejecutar `validate()` para comprobar los campos antes de procesarlos.
- **No liberar los controladores.** Los `TextEditingController` creados por nuestro objeto `State` deben liberarse en `dispose()`.

Una aplicación debe proporcionar mensajes claros cuando los datos son incorrectos. Mostrar simplemente que ha ocurrido un error, sin explicar qué debe corregir el usuario, dificulta completar la tarea.

### 7.6. Ampliación: limpiar el formulario y pedir confirmación

> **Apartado de ampliación.** Te servirá para la ampliación voluntaria del Reto 4.2, que propone añadir un botón para limpiar el formulario.

Vamos a completar el formulario de registro del apartado 7.4 con dos mejoras:

- Un botón **Limpiar** que vacía los campos y quita el resultado de la pantalla.
- Un **diálogo de confirmación** que pregunta antes de borrar, para que el usuario no pierda los datos por una pulsación accidental.

Además, en lugar de mostrar la ficha en un `SnackBar`, la guardaremos en el estado y la dejaremos visible debajo del formulario. Así tendremos un resultado que limpiar.

#### Vaciar los campos con clear()

Cada `TextEditingController` tiene el método `clear()`, que borra el texto del campo asociado:

```
_nombreController.clear();
_edadController.clear();
```

Si usas un `Form`, llama también a `reset()` sobre su estado. Así el formulario vuelve a su situación inicial y desaparecen los mensajes de error que hubiera mostrado la validación:

```
_formKey.currentState!.reset();
```

Vaciar los campos no basta si la pantalla muestra un resultado guardado en una variable de estado. Para quitarlo, hay que cambiar esa variable dentro de `setState()`, igual que hacíamos con el contador del capítulo 5:

```dart
setState(() {
  _ficha = null;
});
```

#### Pedir confirmación con AlertDialog

Un `AlertDialog` es una ventana que aparece sobre la pantalla y no desaparece hasta que el usuario responde. Para mostrarlo se utilizan tres piezas:

- `showDialog()` abre la ventana. Recibe el `context` de la pantalla y una función `builder` que construye el diálogo.
- `AlertDialog` define su contenido: un `title`, un `content` y una lista de botones en `actions`.
- `Navigator.of(dialogContext).pop()` cierra el diálogo. `Navigator` gestiona las ventanas que se abren sobre la pantalla; aquí solo lo usamos para cerrarlo.

El diálogo no necesita `setState()`: se abre y se cierra por sí mismo. Solo hace falta `setState()` en `_limpiar()`, porque cambia el estado de la pantalla.

#### Ejemplo completo

Abre `lib/main.dart` en un proyecto de prueba y sustituye su contenido por el siguiente código:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const FormularioAmpliadoApp());
}

class FormularioAmpliadoApp extends StatelessWidget {
  const FormularioAmpliadoApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: PantallaFormularioAmpliado(),
    );
  }
}

class PantallaFormularioAmpliado extends StatefulWidget {
  const PantallaFormularioAmpliado({super.key});

  @override
  State<PantallaFormularioAmpliado> createState() =>
      _PantallaFormularioAmpliadoState();
}

class _PantallaFormularioAmpliadoState
    extends State<PantallaFormularioAmpliado> {
  final _formKey = GlobalKey<FormState>();
  final _nombreController = TextEditingController();
  final _edadController = TextEditingController();

  String? _ficha;

  @override
  void dispose() {
    _nombreController.dispose();
    _edadController.dispose();
    super.dispose();
  }

  void _guardar() {
    if (!_formKey.currentState!.validate()) {
      return;
    }

    final nombre = _nombreController.text.trim();
    final edad = int.parse(_edadController.text.trim());

    setState(() {
      _ficha = 'Ficha creada: $nombre, $edad años.';
    });
  }

  void _limpiar() {
    _nombreController.clear();
    _edadController.clear();
    _formKey.currentState!.reset();

    setState(() {
      _ficha = null;
    });
  }

  void _confirmarLimpieza() {
    showDialog<void>(
      context: context,
      builder: (dialogContext) {
        return AlertDialog(
          title: const Text('Borrar los datos'),
          content: const Text(
            'Se vaciarán los campos y la ficha. ¿Quieres continuar?',
          ),
          actions: [
            TextButton(
              onPressed: () {
                Navigator.of(dialogContext).pop();
              },
              child: const Text('Cancelar'),
            ),
            TextButton(
              onPressed: () {
                Navigator.of(dialogContext).pop();
                _limpiar();
              },
              child: const Text('Borrar'),
            ),
          ],
        );
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Formulario con limpieza'),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(20),
        child: Form(
          key: _formKey,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              TextFormField(
                controller: _nombreController,
                decoration: const InputDecoration(
                  labelText: 'Nombre',
                  border: OutlineInputBorder(),
                ),
                validator: (valor) {
                  if (valor == null || valor.trim().isEmpty) {
                    return 'Introduce tu nombre';
                  }
                  return null;
                },
              ),

              const SizedBox(height: 16),

              TextFormField(
                controller: _edadController,
                keyboardType: TextInputType.number,
                decoration: const InputDecoration(
                  labelText: 'Edad',
                  border: OutlineInputBorder(),
                ),
                validator: (valor) {
                  final edad = int.tryParse(valor?.trim() ?? '');
                  if (edad == null || edad <= 0) {
                    return 'Introduce una edad válida';
                  }
                  return null;
                },
              ),

              const SizedBox(height: 24),

              Row(
                children: [
                  Expanded(
                    child: ElevatedButton(
                      onPressed: _guardar,
                      child: const Text('Guardar'),
                    ),
                  ),
                  const SizedBox(width: 12),
                  Expanded(
                    child: OutlinedButton(
                      onPressed: _confirmarLimpieza,
                      child: const Text('Limpiar'),
                    ),
                  ),
                ],
              ),

              const SizedBox(height: 24),

              if (_ficha != null)
                Text(
                  _ficha!,
                  style: const TextStyle(
                    fontSize: 18,
                    fontWeight: FontWeight.bold,
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

Ejecuta la aplicación con `flutter run -d chrome`, si Chrome está disponible como dispositivo. Guarda una ficha, pulsa **Limpiar** y prueba las dos respuestas del diálogo.

Observa algunos elementos nuevos:

- `String? _ficha` declara una variable que puede valer `null`. Mientras valga `null`, no hay ficha que mostrar.
- `if (_ficha != null)` dentro de `children` añade el `Text` solo cuando existe una ficha. Si la condición no se cumple, el widget no forma parte de la lista.
- `_ficha!` indica a Dart que, en ese punto, la variable no es `null`. Podemos afirmarlo porque la condición anterior ya lo ha comprobado.
- El botón **Limpiar** no llama directamente a `_limpiar()`, sino a `_confirmarLimpieza()`. Solo el botón **Borrar** del diálogo ejecuta la limpieza, después de cerrar la ventana.
- Usamos `dialogContext`, el contexto que recibe el `builder`, para cerrar el diálogo. Así cerramos la ventana que acabamos de abrir y no la pantalla.

#### ¿SnackBar o AlertDialog?

Los dos muestran mensajes, pero responden a situaciones distintas:

- **`SnackBar`:** informa de algo que ya ha ocurrido, como *Datos guardados*. Desaparece solo y el usuario puede seguir trabajando sin responder.
- **`AlertDialog`:** interrumpe para pedir una decisión o avisar de algo importante, como confirmar un borrado. El usuario tiene que responder para continuar.

Usa el diálogo solo cuando la respuesta importe. Si cada acción abre una ventana, el usuario acabará pulsando sin leer.

**Comprueba lo aprendido:** introduce una edad incorrecta para que aparezca el mensaje de error y pulsa **Limpiar**. Comprueba que, al confirmar, desaparecen los datos, la ficha y el mensaje de error, y que al pulsar **Cancelar** no cambia nada.

**Documentación de consulta:** [Validación de formularios en Flutter](https://docs.flutter.dev/cookbook/forms/validation), [obtener el contenido de un campo](https://docs.flutter.dev/cookbook/forms/retrieve-input), [referencia de TextFormField](https://api.flutter.dev/flutter/material/TextFormField-class.html) y [referencia de AlertDialog](https://api.flutter.dev/flutter/material/AlertDialog-class.html).
