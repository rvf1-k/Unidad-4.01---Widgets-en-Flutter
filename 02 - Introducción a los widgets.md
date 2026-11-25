# 2. Introducción a los widgets

*Fuente: [Unidad 4.01 - Widgets en Flutter - 2. Introducción a los widgets](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76809)*

### 2.1. ¿Qué es un widget?

Un **widget** describe una parte de la interfaz. Puede mostrar contenido, organizar otros widgets o permitir que el usuario interactúe con la aplicación.

- `Text` muestra un texto.
- `Icon` representa un icono.
- `Row` y `Column` distribuyen elementos horizontal y verticalmente.
- `Padding` añade espacio alrededor de otro widget.
- `Scaffold` proporciona la estructura habitual de una pantalla Material.

Los widgets se colocan unos dentro de otros y forman un **árbol de widgets**. Esta idea ya la conoces del *Widget Tree* de FlutterFlow: ahora trabajarás directamente con él desde Dart.

### 2.2. Pensar de forma declarativa

Flutter utiliza un modelo **declarativo**: el código describe qué interfaz debe mostrarse con los datos actuales. Cuando estos cambian, Flutter vuelve a construir la descripción de los widgets que corresponda. No tienes que ir modificando manualmente cada elemento de la pantalla.

Por ejemplo, `Text('Hola')` describe un texto que debe aparecer en la interfaz. Más adelante aprenderás a cambiar lo que se muestra mediante el estado de la aplicación.

### 2.3. Primer ejemplo completo

Vamos a crear nuestra primera pantalla utilizando varios widgets. Puedes trabajar en un proyecto Flutter de prueba independiente para conservar los ejemplos del capítulo anterior. Abre `lib/main.dart` y sustituye su contenido por este ejemplo:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const EjemploWidgetsApp());
}

class EjemploWidgetsApp extends StatelessWidget {
  const EjemploWidgetsApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: PantallaInicial(),
    );
  }
}

class PantallaInicial extends StatelessWidget {
  const PantallaInicial({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Primeros widgets'),
      ),
      body: const Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Icon(Icons.widgets_outlined, size: 48),
            SizedBox(height: 12),
            Text(
              'Una pantalla se construye con widgets.',
              textAlign: TextAlign.center,
            ),
          ],
        ),
      ),
    );
  }
}
```

Desde la terminal del proyecto puedes ejecutarlo con `flutter run -d chrome`, si Chrome está configurado como dispositivo.

Fíjate en cuatro elementos:

- `runApp()` inicia la aplicación con su widget raíz.
- `MaterialApp` configura la aplicación Material y establece la pantalla inicial mediante `home`.
- `Scaffold` organiza la barra superior y el contenido de la pantalla.
- El método `build()` devuelve los widgets que describen cada parte de la interfaz.

### 2.4. Lee el árbol de widgets

La pantalla anterior tiene esta estructura simplificada:

```
MaterialApp
└── PantallaInicial
    └── Scaffold
        ├── AppBar
        │   └── Text
        └── Center
            └── Column
                ├── Icon
                ├── SizedBox
                └── Text
```

Una propiedad llamada `child` recibe un widget; `children` recibe varios. El árbol permite entender qué elemento contiene a cada uno. La construcción de pantallas más complejas consiste, en buena parte, en organizar correctamente esas relaciones.

### Documentación de consulta

- [Flutter: catálogo oficial de widgets](https://docs.flutter.dev/ui/widgets).
- [Flutter: interfaces declarativas](https://docs.flutter.dev/flutter-for/declarative).
