# 1. Estructura de una aplicación Flutter

*Fuente: [Unidad 4.01 - Widgets en Flutter - 1. Estructura de una aplicación Flutter](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76810)*

En un primer proyecto podemos crear una aplicación sencilla escribiendo todo el código en `main.dart`. Esto funciona para empezar, pero cuando una aplicación crece conviene organizarla en varios archivos.

Flutter utiliza una estructura de proyecto que separa el código de la aplicación, las dependencias, los recursos y las pruebas.

Antes de estudiar esta estructura, vamos a aprender a crear un proyecto desde Visual Studio Code, ejecutarlo y utilizar las herramientas que facilitan el desarrollo.

### 1.1. Crear un proyecto Flutter desde Visual Studio Code

Visual Studio Code (VS Code) es el entorno de desarrollo que utilizaremos para programar nuestras aplicaciones Flutter. Nos permite escribir código Dart, organizar los archivos del proyecto, ejecutar aplicaciones y localizar errores.

Para trabajar con Flutter necesitamos tener instalado el SDK de Flutter y las extensiones de Flutter y Dart para VS Code. En los ordenadores del aula el entorno ya está preparado, por lo que no es necesario volver a instalarlo.

#### Crear una aplicación nueva

Sigue estos pasos:

1. Abre Visual Studio Code.
2. Abre la paleta de comandos desde **View > Command Palette**. También puedes utilizar **Ctrl + Shift + P** en Windows o Linux, o **Cmd + Shift + P** en macOS.
3. Escribe **Flutter: New Project** y selecciona ese comando.
4. Elige **Empty Application** como tipo de proyecto.
5. Selecciona la carpeta donde quieres guardar tus proyectos. Flutter creará automáticamente una carpeta específica para la nueva aplicación.
6. Introduce un nombre válido, por ejemplo `mi_primera_app`. Utiliza letras minúsculas y guiones bajos, sin espacios.
7. Cuando termine la creación, abre el proyecto generado y localiza el archivo `lib/main.dart`.

Con **Empty Application**, Flutter genera un `main.dart` mínimo, que muestra el texto *Hello World!*, y los archivos necesarios para ejecutarlo. Partimos de esta plantilla porque no incluye código de ejemplo que tengamos que borrar. No tienes que crear manualmente toda la estructura del proyecto.

#### Conocer el entorno de Visual Studio Code

Antes de empezar a programar, identifica estas partes del editor:

- **Explorador de archivos:** aparece normalmente en la parte izquierda y permite abrir las carpetas y archivos del proyecto.
- **Editor de código:** es la zona central donde escribiremos las instrucciones Dart.
- **Terminal integrada:** permite ejecutar comandos de Flutter sin salir de VS Code. Puedes abrirla desde **Terminal > New Terminal**.
- **Run and Debug:** contiene herramientas para ejecutar la aplicación, detenerla y examinar su funcionamiento.
- **Problems** y **Debug Console:** permiten consultar problemas detectados por el editor y mensajes generados durante la depuración.
- **Barra de estado:** en un proyecto Flutter muestra información del entorno y permite seleccionar el dispositivo de ejecución.

Cuando trabajemos con varios archivos, utilizaremos principalmente el explorador, el editor y la terminal. Más adelante aprenderemos a utilizar las herramientas de depuración con mayor detalle.

**Comprueba lo aprendido:** crea un proyecto llamado `prueba_flutter`, abre `main.dart` y localiza `pubspec.yaml` en el explorador. No es necesario modificar todavía su contenido.

### 1.2. Ejecutar Flutter en un navegador o emulador

Una aplicación Flutter puede ejecutarse en diferentes dispositivos y plataformas. Durante las prácticas utilizaremos principalmente Chrome y, cuando esté disponible, un emulador de Android.

Un emulador reproduce el funcionamiento de un dispositivo Android en el ordenador. Permite probar una aplicación sin necesidad de disponer de un teléfono físico.

#### Seleccionar un dispositivo

1. Abre un proyecto Flutter en VS Code.
2. Localiza el selector de dispositivos en la barra de estado.
3. Haz clic sobre el dispositivo seleccionado para consultar los destinos disponibles.
4. Selecciona Chrome o el emulador Android que tengas disponible.
5. Inicia la ejecución desde **Run > Start Debugging** o pulsando **F5**.

Si aparece **No Devices**, significa que Flutter no ha detectado ningún dispositivo disponible en ese momento. Comprueba que Chrome o el emulador estén correctamente configurados.

#### Trabajar con un emulador Android

Los emuladores Android se gestionan habitualmente desde Android Studio, mediante **Device Manager**. Desde esta herramienta podemos iniciar un dispositivo virtual que ya esté configurado.

Una vez iniciado, debería aparecer entre los dispositivos disponibles de Flutter. Selecciónalo desde VS Code y ejecuta la aplicación.

Ten en cuenta que el primer arranque de un emulador o de una aplicación puede tardar más que las ejecuciones posteriores. Además, un emulador consume más recursos del ordenador que la ejecución en Chrome.

#### Ejecutar desde la terminal

También puedes trabajar directamente con comandos Flutter.

Para consultar los dispositivos disponibles:

```
flutter devices
```

Para consultar los emuladores configurados:

```
flutter emulators
```

Para ejecutar la aplicación en Chrome:

```
flutter run -d chrome
```

Este último comando necesita que Chrome esté disponible como destino de ejecución. Si no lo está, utiliza otro dispositivo configurado.

**Comprueba lo aprendido:** ejecuta la aplicación generada primero en Chrome y después, si está disponible, en un emulador Android. Observa cómo se adapta la interfaz a cada pantalla.

### 1.3. Hot Reload y Hot Restart

Durante el desarrollo modificaremos continuamente el código para cambiar textos, tamaños, colores y otros elementos de la interfaz. Flutter proporciona dos herramientas que permiten comprobar estos cambios sin tener que iniciar todo el proceso desde cero.

#### Hot Reload

**Hot Reload** permite aplicar muchos cambios de código a una aplicación que ya está ejecutándose en modo depuración, normalmente conservando su estado actual.

Por ejemplo, si modificas el texto o el color de un widget `Text`, puedes aplicar Hot Reload para ver el cambio en la pantalla sin reiniciar completamente la aplicación.

Cuando ejecutas Flutter desde VS Code en modo depuración, puedes utilizar la opción **Hot Reload** de la barra de herramientas. También puedes configurarlo para que se realice al guardar los archivos Dart.

#### Hot Reload al guardar

VS Code permite configurar cuándo debe solicitarse Hot Reload. Si no se ejecuta automáticamente al guardar, comprueba la configuración de la extensión de Dart y Flutter.

Por ejemplo, estas opciones pueden añadirse a `.vscode/settings.json` dentro del proyecto:

```
{
  "files.autoSave": "afterDelay",
  "dart.flutterHotReloadOnSave": "all"
}
```

`files.autoSave` permite guardar automáticamente los archivos después de un breve periodo sin escribir. `dart.flutterHotReloadOnSave` configura la aplicación de Hot Reload al guardar durante el desarrollo.

Estas opciones son una configuración de ejemplo y no es necesario añadirlas si el entorno del aula ya funciona correctamente. En cualquier caso, la aplicación debe estar ejecutándose en un modo compatible con Hot Reload.

#### Hot Restart

**Hot Restart** reinicia la ejecución de la aplicación y vuelve a ejecutar su función `main()`. A diferencia de Hot Reload, no conserva el estado que estaba almacenado en memoria.

Algunos cambios, como determinadas modificaciones en `main()` o en la inicialización de variables, pueden necesitar Hot Restart para aplicarse correctamente.

Desde VS Code puedes utilizar la opción **Hot Restart** de las herramientas de depuración.

**Comprueba lo aprendido:** ejecuta tu aplicación, modifica un texto visible y aplica Hot Reload. Después realiza un Hot Restart y observa la diferencia. Más adelante veremos cómo se conserva o reinicia el estado de una aplicación.

### 1.4. Organización de un proyecto Flutter

Cuando creas un proyecto Flutter como **Empty Application**, encontrarás una estructura similar a esta:

```
mi_aplicacion/
├── lib/
│   └── main.dart
├── android/
├── ios/
├── web/
├── pubspec.yaml
└── pubspec.lock
```

Los archivos y carpetas más importantes son:

- `lib/`: contiene el código Dart de la aplicación. Aquí desarrollarás las pantallas y los componentes.
- `main.dart`: es el punto de entrada habitual. Contiene la función `main()`, desde la que se inicia la aplicación.
- `pubspec.yaml`: define el nombre del proyecto, sus dependencias y los recursos que utiliza.
- `android/`, `ios/` y `web/`: contienen la configuración y los archivos necesarios para ejecutar la aplicación en las plataformas correspondientes.

Esta plantilla no crea la carpeta `test/`. Cuando escribamos pruebas automatizadas, en la segunda parte de la unidad, la crearemos en la raíz del proyecto, al mismo nivel que `lib/`.

También encontrarás carpetas generadas automáticamente, como `.dart_tool/` y `build/`. No necesitas modificar su contenido.

### 1.5. El papel de main.dart

La ejecución comienza en la función `main()`, que normalmente llama a `runApp()` para mostrar el widget raíz.

En nuestro proyecto inicial, `main.dart` contiene tanto la configuración de la aplicación como la pantalla inicial.

Podemos separar estas responsabilidades para que el código sea más fácil de mantener:

- `main.dart` inicia y configura la aplicación.
- Un archivo independiente contiene la pantalla.
- Otros archivos pueden contener componentes que utilizaremos en diferentes partes de la interfaz.

No existe una única organización obligatoria. Lo importante es que cada archivo tenga una responsabilidad clara.

### 1.6. Separar una pantalla en otro archivo

Vamos a reorganizar el proyecto inicial utilizando dos archivos.

Primero, crea una carpeta `screens` dentro de `lib`. Después, crea el archivo `pantalla_inicio.dart`.

La estructura quedará así:

```
lib/
├── main.dart
└── screens/
    └── pantalla_inicio.dart
```

#### Archivo lib/main.dart

```dart
import 'package:flutter/material.dart';
import 'screens/pantalla_inicio.dart';

void main() {
  runApp(const MiAplicacion());
}

class MiAplicacion extends StatelessWidget {
  const MiAplicacion({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: PantallaInicio(),
    );
  }
}
```

#### Archivo lib/screens/pantalla_inicio.dart

```dart
import 'package:flutter/material.dart';

class PantallaInicio extends StatelessWidget {
  const PantallaInicio({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Inicio'),
      ),
      body: const Center(
        child: Text('Mi primera pantalla separada'),
      ),
    );
  }
}
```

Fíjate en tres aspectos:

- `import 'screens/pantalla_inicio.dart';` permite utilizar en `main.dart` una clase definida en otro archivo.
- `home: PantallaInicio()` establece qué pantalla se mostrará al iniciar la aplicación.
- `PantallaInicio` es un widget personalizado que hemos definido mediante una clase. Más adelante aprenderás a crear componentes que reciban propiedades y puedan reutilizarse.

El resultado es una aplicación funcional cuyo código está repartido en dos archivos.

### 1.7. Organizar pantallas y componentes

A medida que una aplicación crece, podemos crear carpetas para agrupar el código según su función.

Por ejemplo:

```
lib/
├── main.dart
├── screens/
│   ├── pantalla_inicio.dart
│   └── pantalla_detalle.dart
├── widgets/
│   ├── tarjeta_info.dart
│   └── boton_personalizado.dart
└── utils/
    └── calculos.dart
```

Esta organización es una propuesta sencilla, no una estructura obligatoria de Flutter.

- `screens/`: contiene las pantallas completas.
- `widgets/`: agrupa componentes reutilizables, como tarjetas o botones.
- `utils/`: puede contener funciones auxiliares, por ejemplo cálculos que no necesitan acceder a la interfaz.

Separar el código facilita localizar errores, reutilizar componentes y trabajar sobre una parte de la aplicación sin modificar las demás.

También podemos organizar nuestros widgets mediante carpetas como `layouts/` y `components/`. Por ejemplo, `layouts/` puede contener widgets propios encargados de distribuir el contenido de una pantalla, mientras que `components/` puede agrupar elementos reutilizables como etiquetas y tarjetas.

Estas carpetas no tienen un funcionamiento especial dentro de Flutter. Son una forma de organizar el código. En el Reto 4.0 utilizamos precisamente esta distribución para practicar la separación entre la organización de una pantalla y sus componentes. En el resto de la unidad usaremos `screens/`, `widgets/` y `utils/`.

### 1.8. Ejecutar y comprobar el proyecto

Para ejecutar el ejemplo, abre la carpeta raíz del proyecto en Visual Studio Code, es decir, la que contiene `pubspec.yaml`.

Desde la terminal:

```
flutter run -d chrome
```

Si has añadido o modificado dependencias en `pubspec.yaml`, actualízalas previamente con:

```
flutter pub get
```

Comprueba que la aplicación muestra la pantalla **Inicio** y el mensaje **Mi primera pantalla separada**.

A partir de ahora, no será necesario escribir todos los widgets en `main.dart`. Podremos crear pantallas y componentes propios y utilizarlos desde otros archivos mediante `import`.

**Documentación de consulta:** [Guía oficial de arquitectura de aplicaciones Flutter](https://docs.flutter.dev/app-architecture/guide), [Flutter en Visual Studio Code](https://docs.flutter.dev/tools/vs-code), [Crear una aplicación Flutter](https://docs.flutter.dev/reference/create-new-app), [Emuladores y desarrollo Android](https://docs.flutter.dev/platform-integration/android/setup) y [Hot Reload y Hot Restart](https://docs.flutter.dev/tools/hot-reload).
