Prototipo interactivo de realidad aumentada para visualizar un papel
tapiz sobre una pared usando la cámara del iPhone/iPad. El prototipo
está implementado como una página web optimizada para interacción táctil
y pantalla completa en iOS.

Estado: prototipo funcional / prueba de concepto.
Archivo principal: prototipo_papel_tapiz.html

1. Objetivo

La experiencia permite apuntar la cámara hacia una pared, seleccionar
manualmente sus cuatro esquinas y proyectar un patrón de papel tapiz
sobre esa superficie respetando su perspectiva.

El prototipo también permite utilizar una fotografía en lugar de la
cámara, ajustar parámetros de la pared y comparar temporalmente el
resultado con la imagen original.

El flujo inicial se presenta como "Papel tapiz en tu pared" y ofrece
las opciones "Abrir cámara" y "Usar una foto".

2. Flujo de usuario

Abrir el prototipo en un navegador compatible de iOS.

Elegir:

Abrir cámara para trabajar en tiempo real.

Usar una foto para hacer una simulación sobre una imagen
existente.

Apuntar a una pared.

Tocar sus cuatro esquinas.

El sistema ordena los cuatro puntos y genera una transformación de
perspectiva.

Ajustar manualmente las esquinas arrastrándolas si es necesario.

Modificar los parámetros de visualización desde Ajustes.

Usar Ver original para ocultar temporalmente el papel tapiz.

Usar Congelar para detener la actualización de la cámara.

Usar Captura para generar una imagen PNG y compartirla o
guardarla.

La interfaz muestra durante la selección el progreso
Toca las esquinas de la pared (n/4) y, una vez completados los cuatro
puntos, indica Arrastra las esquinas para afinar.

3. Funcionalidades implementadas

Cámara

El prototipo solicita la cámara trasera mediante getUserMedia,
utilizando facingMode: environment y solicitando una resolución ideal
de 1920×1080.

El vídeo se reproduce inline y se procesa mediante WebGL.

Si el acceso a la cámara falla, se muestra un mensaje indicando que se
debe permitir el acceso en Safari o utilizar una fotografía.

Entrada mediante fotografía

Existe una alternativa para seleccionar una imagen desde el dispositivo
mediante un <input type="file" accept="image/*">.

La imagen se escala hasta un máximo de 1600 px en su dimensión mayor
antes de ser utilizada como fuente de renderizado.

Cuando se utiliza una fotografía, el control Congelar se oculta
porque la fuente ya es estática.

Selección manual de la pared

La pared se define mediante cuatro puntos táctiles.

Con menos de cuatro puntos, cada toque añade una esquina.

Al alcanzar cuatro puntos, se ordenan geométricamente.

Los puntos pueden arrastrarse para corregir la alineación.

Reiniciar elimina los cuatro puntos y permite volver a definir
la pared.

La transformación se calcula mediante una homografía 3×3, convirtiendo
un cuadrilátero de pantalla en el espacio de textura del papel tapiz.

Renderizado WebGL

El prototipo utiliza un canvas WebGL como superficie principal.

Se mantienen dos canvas:

gl: renderizado WebGL de cámara + papel tapiz.

ov: capa de interfaz para dibujar las esquinas y el contorno de la
pared.

El shader de fragmentos:

Obtiene la imagen de cámara.

Aplica la homografía para determinar si el píxel está dentro de la
pared seleccionada.

Obtiene el patrón de papel tapiz.

Repite la textura según las dimensiones configuradas.

Estima la iluminación local de la escena.

Aplica brillo y tinte de la escena.

Puede añadir una ligera atenuación en las uniones entre lienzos.

Mezcla el papel tapiz con la imagen original.

La textura de papel tapiz está embebida directamente en el HTML como
JPEG Base64.

Adaptación a iluminación

El prototipo analiza periódicamente una versión reducida de la imagen de
entrada para estimar:

luminancia de la escena;

luminancia aproximada de la región de la pared;

dominante de color de la escena.

Estos valores se utilizan para modificar la apariencia del papel tapiz
antes de mezclarlo con la cámara.

Esto es una aproximación visual; no representa una medición física de
iluminación.

Comparación

Ver original funciona como comparación temporal:

mientras se mantiene pulsado, se muestra la imagen original;

al soltar, vuelve a mostrarse la composición con el papel tapiz.

Congelar

Congelar detiene la actualización de la textura de cámara mientras
conserva la composición actual.

El botón cambia a Descongelar cuando está activo.

Captura

Captura genera un PNG a partir del canvas WebGL.

Si el navegador permite compartir archivos, se intenta utilizar el
mecanismo nativo de compartir de Web Share API. En caso contrario, se
genera una descarga local con el nombre:

papel-tapiz.png

4. Ajustes disponibles

Ajuste                               Valor inicial Función

Ancho de la pared                              3 m Controla la escala
horizontal del patrón

Alto de la pared                             2.6 m Controla la escala
vertical del patrón

Pared actual                                 Clara Estimación de
reflectancia/albedo
de la pared

Adaptar a la luz                              0.85 Intensidad de la
adaptación a
iluminación

Brillo                                         1.0 Multiplicador de
brillo del papel

Los controles de ancho y alto están pensados para aproximar la cantidad
física de papel necesaria visualmente. No constituyen una medición
automática de la pared.

5. Arquitectura actual

iPhone / iPad
    │
    ├── Safari
    │
    ├── Cámara trasera
    │       │
    │       └── HTMLVideoElement
    │
    ├── Canvas WebGL
    │       ├── textura de cámara
    │       ├── textura de papel tapiz
    │       ├── homografía
    │       ├── iluminación aproximada
    │       └── composición
    │
    └── Canvas overlay
            ├── esquinas
            ├── contorno
            └── indicadores de selección

El código está contenido en un único HTML autocontenido: estructura,
estilos, lógica JavaScript, shaders WebGL y textura del papel tapiz.

6. Requisitos para ejecutar el prototipo

Cliente

iPhone o iPad con cámara para el modo cámara.

Navegador con soporte para:

getUserMedia

WebGL

Pointer Events

Web Share API, opcional para compartir

JavaScript habilitado.

Permiso de cámara para el modo en vivo.

Servidor

Para el modo cámara, se debe servir la página desde un contexto seguro
(HTTPS) o desde un entorno local equivalente soportado por el
navegador.

Abrir el HTML directamente como archivo local puede impedir el acceso a
cámara dependiendo del entorno y de las políticas del navegador.

7. Cómo probarlo

Opción A --- Cámara

Servir prototipo_papel_tapiz.html mediante HTTPS.

Abrirlo desde Safari en el iPhone/iPad.

Pulsar Abrir cámara.

Conceder permiso de cámara.

Enfocar una pared.

Tocar las cuatro esquinas.

Arrastrar los puntos para afinar.

Ajustar ancho, alto, luz y brillo.

Probar Ver original.

Probar Congelar.

Probar Captura.

Opción B --- Fotografía

Abrir el prototipo.

Pulsar Usar una foto.

Seleccionar una imagen.

Marcar las cuatro esquinas de la pared.

Ajustar la composición.

Generar una captura.

Esta modalidad es especialmente útil para desarrollo, QA y pruebas sin
permisos de cámara.

8. Limitaciones actuales

Este prototipo no utiliza ARKit ni RealityKit y no realiza tracking
espacial 3D de una pared.

La detección de la superficie es manual:

el usuario selecciona las cuatro esquinas;

la geometría se basa en una homografía 2D;

no existe detección automática de planos;

no existe seguimiento de la posición del dispositivo en el espacio;

no existe estimación de profundidad;

no existe oclusión de objetos frente a la pared;

no existe segmentación automática de puertas, ventanas, muebles u
otros elementos;

no existe reconstrucción 3D de la habitación.

Por tanto, si el objetivo final es una aplicación nativa de iOS con AR
real, este HTML debe considerarse principalmente como prototipo de
UX/renderizado y validación visual.

9. Evolución recomendada hacia una app nativa iOS

La siguiente fase puede migrar el concepto a Swift + RealityKit/ARKit.

Capa de captura y tracking

Sustituir:

getUserMedia + HTMLVideoElement

por:

ARSession
    ↓
ARWorldTrackingConfiguration
    ↓
ARPlaneAnchor / detección de planos
    ↓
superficie de pared

El objetivo sería detectar automáticamente paredes verticales y mantener
el papel tapiz adherido a ellas mientras el usuario mueve el iPhone.

Capa de material

El patrón actual podría convertirse en una textura/material de
RealityKit.

La escala física debería basarse en metros reales en lugar de utilizar
únicamente la aproximación visual del prototipo.

Iluminación

En una implementación nativa se puede utilizar la información de
iluminación de ARKit/RealityKit y las capacidades del dispositivo para
obtener una integración más natural.

Oclusión

La siguiente versión debería considerar:

personas delante de la pared;

muebles;

puertas;

ventanas;

otros objetos que deban aparecer delante del papel tapiz.

Medición

El ancho y alto de la pared podrían pasar de ser parámetros introducidos
manualmente a dimensiones derivadas de la geometría detectada.

10. Estructura funcional objetivo para la app iOS

Una posible evolución del prototipo sería:

App
│
├── Inicio
│   ├── Ver con cámara
│   └── Probar con foto
│
├── Selección de papel tapiz
│   ├── Catálogo
│   ├── Favoritos
│   └── Variantes
│
├── AR Viewer
│   ├── Detección de pared
│   ├── Colocación
│   ├── Escala
│   ├── Rotación
│   ├── Repetición
│   ├── Iluminación
│   └── Oclusión
│
├── Configuración
│   ├── Dimensiones
│   ├── Patrón
│   ├── Brillo
│   └── Uniones
│
└── Resultado
    ├── Captura
    ├── Compartir
    └── Guardar diseño

11. Consideraciones de rendimiento

El prototipo limita el devicePixelRatio utilizado para los canvas a un
máximo de 2.

La textura de entrada de la fotografía se reduce a un máximo de 1600 px
en su dimensión mayor.

La estimación de iluminación utiliza un buffer reducido de 48×27 píxeles
y se actualiza aproximadamente cada 400 ms, en lugar de analizar toda la
imagen en cada frame.

La textura del papel tapiz utiliza filtrado lineal, mipmaps y, cuando
está disponible, filtrado anisotrópico limitado hasta 8×.

Estas decisiones reducen el coste de renderizado en dispositivos
móviles.

12. Privacidad

En el prototipo, la cámara se procesa localmente en el navegador para
generar la composición visual.

No hay backend, autenticación, base de datos ni API de servidor
implementados en este HTML.

Las fotografías seleccionadas se utilizan como fuente local del
renderizado.

La funcionalidad de captura utiliza mecanismos locales del navegador
para generar/compartir el resultado.

13. Próximos pasos

Prioridad 1 --- Convertir el prototipo en app iOS

Swift + RealityKit/ARKit.

Detección automática de paredes.

Tracking persistente.

Anclaje del papel tapiz al plano.

Prioridad 2 --- Mejorar la experiencia visual

Material PBR.

Iluminación ambiental.

Sombras/reflejos coherentes.

Oclusión.

Mejor estimación de color y exposición.

Prioridad 3 --- Producto

Catálogo de papeles tapiz.

Variaciones de patrones.

Escala y repetición.

Metraje estimado.

Precio.

Favoritos.

Compartir diseños.

Guardar proyectos.

Prioridad 4 --- Medición

Medición automática de ancho y alto.

Detección de esquinas.

Detección de puertas y ventanas.

Cálculo de superficie.

Estimación de cantidad de rollos necesarios.

14. Criterios de aceptación del prototipo

El prototipo se considera funcional cuando:

la cámara puede abrirse y mostrar la escena;

el usuario puede seleccionar cuatro esquinas;

el patrón queda proyectado dentro de la superficie seleccionada;

las esquinas pueden corregirse mediante arrastre;

la escala cambia al modificar las dimensiones;

la iluminación puede modificarse;

el modo original muestra la cámara sin el papel tapiz;

el modo congelar detiene la actualización de la escena;

la captura genera un PNG.

15. Nota sobre el estado del proyecto

Este README describe el comportamiento observado en
prototipo_papel_tapiz.html. El archivo actual es una prueba de
concepto web y no debe interpretarse como una implementación final de
ARKit.

La principal función del prototipo es validar la interacción, el flujo
de visualización del papel tapiz y el algoritmo de composición por
perspectiva antes de trasladar la experiencia a una arquitectura nativa
de iOS.
