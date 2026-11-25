# 3. Widgets básicos y composición de interfaces

*Fuente: [Unidad 4.01 - Widgets en Flutter - 3. Widgets básicos y composición de interfaces](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76811)*

Una **prueba de widget** permite construir una parte de la interfaz en un entorno de pruebas, simular una interacción y comprobar el resultado. Utilizaremos el componente `BotonAccion`, creado en el capítulo 6 del primer libro.

### 3.1. Probar un componente reutilizable

Recupera el archivo `lib/widgets/boton_accion.dart` del capítulo 6. El componente recibe un texto y un callback `onPressed`. Esta es su implementación completa:

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

Vamos a comprobar dos cosas: que muestra el texto recibido y que ejecuta su callback cuando se pulsa.

Crea `test/boton_accion_test.dart`. Sustituye `mi_aplicacion` por el nombre del proyecto en `pubspec.yaml`:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mi_aplicacion/widgets/boton_accion.dart';

void main() {
  testWidgets('Muestra el texto y ejecuta la acción', (tester) async {
    int pulsaciones = 0;

    await tester.pumpWidget(
      MaterialApp(
        home: Scaffold(
          body: BotonAccion(
            texto: 'Abrir aviso',
            onPressed: () {
              pulsaciones++;
            },
          ),
        ),
      ),
    );

    expect(find.text('Abrir aviso'), findsOneWidget);

    await tester.tap(find.text('Abrir aviso'));
    await tester.pump();

    expect(pulsaciones, 1);
  });
}
```

Qué hace cada parte:

- `testWidgets()` define una prueba de widget y proporciona un objeto `tester`.
- `pumpWidget()` construye el componente en el entorno de pruebas. Lo envolvemos en `MaterialApp` y `Scaffold` para proporcionarle el contexto Material.
- `find.text()` localiza un widget por su texto; `findsOneWidget` comprueba que aparece una vez.
- `tester.tap()` simula una pulsación. `tester.pump()` procesa el siguiente fotograma y los cambios pendientes.
- `expect(pulsaciones, 1)` comprueba que la función recibida se ha ejecutado una vez.

Ejecuta `flutter test test/boton_accion_test.dart`. Si cambias el texto buscado en el test por otro que no exista, la prueba fallará. Así puedes comprobar que el test detecta un comportamiento diferente al esperado.

### 3.2. Probar campos de texto y resultados

En un formulario también podemos simular lo que escribe el usuario. Vamos a probar el formulario de registro del capítulo 7 del primer libro (apartado 7.4). Comprueba que sus dos `TextFormField` incluyen las claves `Key('campoNombre')` y `Key('campoEdad')`.

Crea `test/formulario_test.dart`. Como el ejemplo está en `main.dart`, importamos ese archivo. Sustituye `mi_aplicacion` por el nombre de tu proyecto:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mi_aplicacion/main.dart';

void main() {
  testWidgets('Muestra errores si el formulario está vacío', (tester) async {
    await tester.pumpWidget(const EjemploFormularioApp());

    await tester.tap(find.text('Guardar'));
    await tester.pump();

    expect(find.text('Introduce tu nombre'), findsOneWidget);
    expect(find.text('Introduce una edad válida'), findsOneWidget);
  });

  testWidgets('Muestra la ficha con datos válidos', (tester) async {
    await tester.pumpWidget(const EjemploFormularioApp());

    await tester.enterText(find.byKey(const Key('campoNombre')), 'Ana');
    await tester.enterText(find.byKey(const Key('campoEdad')), '20');
    await tester.tap(find.text('Guardar'));
    await tester.pump();

    expect(find.text('Ficha creada: Ana, 20 años.'), findsOneWidget);
  });
}
```

Qué hace cada parte:

- `pumpWidget(const EjemploFormularioApp())` construye la aplicación completa. En este caso no hace falta envolverla en `MaterialApp`, porque ya la incluye.
- `find.byKey()` localiza cada campo por su clave. Es más seguro que `find.byType(TextFormField)`, que encontraría los dos campos a la vez.
- `enterText()` escribe en el campo como si lo hiciera el usuario.
- Después de `tap()`, `pump()` reconstruye la pantalla para que aparezcan los mensajes de error o el `SnackBar`.
- Cada `testWidgets()` empieza con una aplicación nueva: lo que se escribe en la primera prueba no pasa a la segunda.

La primera prueba comprueba la validación y la segunda, el resultado con datos correctos. Si cambias `'20'` por `'abc'` en la segunda, fallará: el formulario no mostrará la ficha.

Para tu propia pantalla, ajusta las claves, los datos, el texto del botón y el resultado esperado. Si utilizas `TextField` en lugar de `TextFormField`, las claves funcionan igual.

Después de interactuar se utiliza `expect()` para comprobar el resultado visible. Cuando existen animaciones o varios fotogramas pendientes puede resultar útil `pumpAndSettle()`, pero no es necesario utilizarlo en todos los tests.

### 3.3. Aplicarlo a los retos

**Reto 4.2:** el enunciado exige al menos una prueba con `testWidgets()` que introduzca peso y altura, pulse el botón `Calcular IMC` y compruebe que aparece el resultado esperado. Debes ajustar los `finders` y los datos al diseño real de tu aplicación.

Para comprobar que aparece un componente propio, puedes buscarlo por su clase con `find.byType()`, por ejemplo `expect(find.byType(ResultadoImcCard), findsOneWidget);`. Necesitarás importar en el test el archivo donde está definido.

Esa prueba complementa las dos pruebas unitarias del capítulo anterior. La aplicación debe funcionar en Chrome y todas las pruebas entregadas deben ejecutarse correctamente con `flutter test`.

**Reto 4.1:** el enunciado vigente evalúa el componente `AvisoCard`, sus propiedades, su método y los callbacks. **No exige entregar pruebas automatizadas**. Si quieres ampliar conocimientos, puedes investigar cómo probar también su botón y la acción asociada.

### 3.4. Problemas habituales

- **Import incorrecto:** revisa que el nombre del paquete coincida con `name:` en `pubspec.yaml`.
- **El buscador no encuentra un widget:** comprueba el texto exacto, el tipo y si el elemento está realmente construido en la prueba.
- **La interfaz no cambia tras una pulsación:** puede faltar `pump()` o estar ejecutándose una acción diferente a la esperada.
- **Falla un test antiguo de ejemplo:** revisa si sigue importando una clase que ya no existe en tu aplicación.
- **Un test pasa, pero no comprueba nada útil:** revisa que tenga una condición `expect()` relacionada con el comportamiento que quieres verificar.

### 3.5. Recursos para ampliar

- [Introducción a las pruebas de widget](https://docs.flutter.dev/cookbook/testing/widget/introduction): creación de widgets, buscadores y comprobaciones.
- [Simular pulsaciones y entrada de texto](https://docs.flutter.dev/cookbook/testing/widget/tap-drag): `tap()`, `enterText()` y otras interacciones.
- [Encontrar widgets durante una prueba](https://docs.flutter.dev/cookbook/testing/widget/finders): búsquedas por texto, tipo o clave.
- [Referencia de WidgetTester](https://api.flutter.dev/flutter/flutter_test/WidgetTester-class.html): funciones de prueba disponibles.

Con esto ya tienes las herramientas necesarias para realizar las pruebas previstas en el Reto 4.2. El objetivo no es escribir muchos tests, sino comprobar de forma clara los comportamientos que pide el enunciado.
