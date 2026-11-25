# 4. Creación de widgets personalizados y propiedades por defecto

*Fuente: [Unidad 4.01 - Widgets en Flutter - 4. Creación de widgets personalizados y propiedades por defecto](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76813)*

Hasta ahora hemos construido pantallas combinando widgets como `Text`, `Row`, `Column` y `Container`. Sin embargo, cuando una misma estructura aparece varias veces, escribir todo su código de nuevo resulta poco práctico.

Flutter permite crear **widgets personalizados** que podemos reutilizar en diferentes partes de una aplicación y configurar mediante propiedades.

### 4.1. ¿Por qué crear widgets personalizados?

Imagina una aplicación que muestra varias tarjetas informativas. Todas tienen una estructura parecida: un icono, un título y una descripción. Lo único que cambia es su contenido.

Podríamos repetir el código de cada tarjeta, pero sería más sencillo crear un widget propio que recibiera esos datos como propiedades.

Esto nos permite:

- **Reutilizar código:** crear una estructura una sola vez y utilizarla en distintas pantallas.
- **Mantener la coherencia:** aplicar el mismo diseño a todos los componentes del mismo tipo.
- **Facilitar los cambios:** modificar un componente sin tener que localizar todas sus copias.

Un widget personalizado se define mediante una clase que normalmente extiende `StatelessWidget` o `StatefulWidget`, según necesite o no gestionar un estado mutable propio.

### 4.2. Crear un widget con propiedades

Vamos a crear un componente sencillo llamado `EtiquetaInformativa`. Mostrará un título y una descripción que podremos cambiar cada vez que lo utilicemos.

Dentro de `lib`, crea la carpeta `widgets` y el archivo `etiqueta_informativa.dart`.

```dart
import 'package:flutter/material.dart';

class EtiquetaInformativa extends StatelessWidget {
  final String titulo;
  final String descripcion;

  const EtiquetaInformativa({
    super.key,
    required this.titulo,
    required this.descripcion,
  });

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(
          titulo,
          style: const TextStyle(
            fontSize: 18,
            fontWeight: FontWeight.bold,
          ),
        ),
        const SizedBox(height: 4),
        Text(descripcion),
      ],
    );
  }
}
```

Observa las partes principales del componente:

- `final String titulo` y `final String descripcion` definen las propiedades que recibirá el widget.
- `required` indica que esas propiedades deben proporcionarse al crear el componente.
- `this.titulo` y `this.descripcion` permiten inicializar las propiedades desde el constructor.
- `super.key` permite recibir una clave opcional que Flutter puede utilizar para identificar el widget dentro del árbol.
- `build()` construye la interfaz utilizando los valores recibidos.

La palabra `final` indica que la propiedad no puede reasignarse después de inicializarse. Sin embargo, el widget padre puede crear una nueva configuración del componente con otros valores.

### 4.3. Reutilizar un componente

Una vez definido nuestro widget, podemos utilizarlo desde otra pantalla. Solo necesitamos importar el archivo correspondiente.

Por ejemplo, dentro de una `Column` podríamos añadir:

```
const EtiquetaInformativa(
  titulo: 'Documentación',
  descripcion: 'Consulta los materiales del curso.',
),

const SizedBox(height: 20),

const EtiquetaInformativa(
  titulo: 'Ejercicios',
  descripcion: 'Practica los contenidos aprendidos.',
),
```

Hemos utilizado el mismo componente dos veces, pero con contenidos diferentes.

Si modificamos la presentación de `EtiquetaInformativa`, el cambio se reflejará en todas sus instancias. No será necesario editar cada una por separado.

También utilizamos `const` porque los valores de estos ejemplos son constantes. Cuando una propiedad dependa de datos que se conocen durante la ejecución, podremos crear el widget sin `const`.

### 4.4. Propiedades opcionales y valores por defecto

No todas las propiedades tienen que ser obligatorias. En algunos casos queremos ofrecer una configuración predeterminada que pueda cambiarse cuando sea necesario.

Vamos a mejorar nuestro componente añadiendo dos propiedades y un método:

- `icono`: permitirá elegir el icono que acompañará al texto.
- `color`: permitirá personalizar el color del icono y, a partir de él, el del fondo.
- `_colorFondo()`: un método propio del componente que calcula el color de fondo.

Si no indicamos estos valores al crear el widget, se utilizarán los definidos por defecto.

Sustituye el contenido de `lib/widgets/etiqueta_informativa.dart` por esta versión:

```dart
import 'package:flutter/material.dart';

class EtiquetaInformativa extends StatelessWidget {
  final String titulo;
  final String descripcion;
  final IconData icono;
  final Color color;

  const EtiquetaInformativa({
    super.key,
    required this.titulo,
    required this.descripcion,
    this.icono = Icons.info_outline,
    this.color = Colors.blue,
  });

  Color _colorFondo() {
    return color.withValues(alpha: 0.08);
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(
        color: _colorFondo(),
        borderRadius: BorderRadius.circular(8),
      ),
      child: Row(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Icon(
            icono,
            color: color,
            size: 28,
          ),
          const SizedBox(width: 12),
          Expanded(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text(
                  titulo,
                  style: const TextStyle(
                    fontSize: 18,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                const SizedBox(height: 4),
                Text(descripcion),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

Fíjate en cómo hemos definido el constructor:

```
const EtiquetaInformativa({
  super.key,
  required this.titulo,
  required this.descripcion,
  this.icono = Icons.info_outline,
  this.color = Colors.blue,
});
```

La diferencia entre las propiedades es importante:

- **Obligatorias:** `titulo` y `descripcion`. Debemos proporcionar un valor para ambas.
- **Opcionales:** `icono` y `color`. Podemos omitirlas porque ya tienen un valor por defecto.

En Dart, los parámetros con valores por defecto deben utilizar valores constantes en tiempo de compilación. Por eso podemos emplear `Icons.info_outline` y `Colors.blue` directamente en el constructor.

Ahora podemos crear dos instancias distintas:

```
const EtiquetaInformativa(
  titulo: 'Información',
  descripcion: 'Consulta los recursos disponibles.',
),

const EtiquetaInformativa(
  titulo: 'Actividad completada',
  descripcion: 'Has terminado el ejercicio.',
  icono: Icons.check_circle_outline,
  color: Colors.green,
),
```

La primera utiliza el icono y el color predeterminados. La segunda proporciona valores diferentes sin modificar el código interno del componente.

#### Métodos del componente

Además de propiedades, un widget puede tener métodos propios. En este ejemplo, `_colorFondo()` toma el color recibido y devuelve una versión casi transparente para el fondo del `Container`.

- El guion bajo inicial indica que el método es privado: solo se utiliza dentro de este archivo.
- Se llama desde `build()`, en `color: _colorFondo()`. Un método que no se llama en ningún sitio no aporta nada al componente.
- Como depende de la propiedad `color`, la etiqueta verde tendrá un fondo verde claro y la azul, uno azul claro, sin escribir dos veces el mismo cálculo.

`withValues(alpha: 0.08)` crea una copia del color con un 8 % de opacidad. Si encuentras ejemplos con `withOpacity()`, es la forma anterior: sigue funcionando, pero Flutter la marca como desaconsejada.

Separar este cálculo en un método mantiene `build()` más legible. Cuando un componente necesite decidir un texto, un color o un icono según sus propiedades, conviene hacerlo de esta manera.

Un método también puede devolver un widget. Por ejemplo, `Widget _construirTitulo()` podría devolver el `Text` del título con su estilo, y `build()` lo llamaría en el lugar donde debe aparecer. Así se divide un componente largo en partes con nombre.

También podríamos utilizar propiedades opcionales que admitan `null`, pero en este ejemplo los valores por defecto permiten mantener un comportamiento sencillo y predecible.

### 4.5. Ejemplo completo: utilizar el widget desde otra pantalla

Vamos a comprobar el funcionamiento del componente dentro de una aplicación.

La estructura del proyecto será la siguiente:

```
lib/
├── main.dart
└── widgets/
    └── etiqueta_informativa.dart
```

Conserva en `etiqueta_informativa.dart` el código completo del apartado 4.4.

Después, sustituye el contenido de `lib/main.dart` por este ejemplo:

```dart
import 'package:flutter/material.dart';
import 'widgets/etiqueta_informativa.dart';

void main() {
  runApp(const EjemploComponentesApp());
}

class EjemploComponentesApp extends StatelessWidget {
  const EjemploComponentesApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: PantallaComponentes(),
    );
  }
}

class PantallaComponentes extends StatelessWidget {
  const PantallaComponentes({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Componentes reutilizables'),
      ),
      body: const Padding(
        padding: EdgeInsets.all(20),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              'Panel de información',
              style: TextStyle(
                fontSize: 24,
                fontWeight: FontWeight.bold,
              ),
            ),
            SizedBox(height: 24),
            EtiquetaInformativa(
              titulo: 'Documentación',
              descripcion: 'Consulta los apuntes de Flutter.',
            ),
            SizedBox(height: 24),
            EtiquetaInformativa(
              titulo: 'Actividad completada',
              descripcion: 'Has finalizado el ejercicio.',
              icono: Icons.check_circle_outline,
              color: Colors.green,
            ),
          ],
        ),
      ),
    );
  }
}
```

Ejecuta la aplicación con `flutter run -d chrome`, si Chrome está disponible como dispositivo.

Deberías observar dos elementos informativos con la misma estructura, pero con diferentes textos, iconos y colores.

Comprueba que:

- Ambas instancias utilizan la clase `EtiquetaInformativa`.
- La primera utiliza los valores por defecto de `icono` y `color`.
- La segunda personaliza esas propiedades.
- El diseño de ambas depende de un único archivo.

Si necesitas cambiar la separación entre el icono y el texto, basta con modificar el `SizedBox` dentro del componente. El cambio afectará a las dos instancias.

**Documentación de consulta:** [Clase StatelessWidget](https://api.flutter.dev/flutter/widgets/StatelessWidget-class.html) y [constructores y parámetros en Dart](https://dart.dev/language/constructors).
