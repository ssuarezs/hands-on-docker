# 🧠 Lab 1: Respuestas a las preguntas de reflexión

1. ¿Qué diferencia hay entre una imagen y un contenedor?
    - Imagen: Es un archivo de solo lectura, una plantilla inmutable que contiene todo lo necesario para ejecutar una aplicación (sistema de archivos, binarios, dependencias, configuraciones).

    - Contenedor: Es una instancia en ejecución de una imagen. Es mutable, tiene su propio sistema de archivos y estado. Podés pensar en la imagen como una clase, y el contenedor como un objeto creado a partir de esa clase.

2. ¿Qué sucede cuando eliminas un contenedor pero no la imagen?
    - La imagen sigue disponible localmente y puedes volver a crear nuevos contenedores basados en ella.

    - El contenedor eliminado pierde cualquier cambio interno que haya tenido (archivos nuevos, logs, etc.), a menos que hayas usado volúmenes para persistencia.

3. ¿Por qué es útil el modo interactivo (-it)?
    - Permite interactuar directamente con el contenedor a través de una terminal (como si fuera SSH).

    - Ideal para aprendizaje, debugging, exploración del entorno interno del contenedor, o pruebas manuales.

    - Combina -i (modo interactivo) con -t (terminal tipo TTY), permitiendo sesiones de shell como sh, bash, etc.

4. ¿Cuál es el propósito de docker ps vs docker ps -a?
    - docker ps: muestra solo los contenedores en ejecución en este momento.

    - docker ps -a: muestra todos los contenedores, incluyendo los que han terminado o están detenidos. Muy útil para depuración o limpieza.

5. ¿Por qué es importante entender el ciclo de vida de las imágenes y contenedores?
    - Te permite manejar bien tu entorno Docker: saber qué eliminar, qué reutilizar, y cómo evitar acumulación de recursos innecesarios (contenedores detenidos, imágenes huérfanas).

    - Ayuda a evitar errores comunes como “no se puede eliminar la imagen porque tiene contenedores activos”.

    - Te prepara para usar Docker con buenas prácticas desde el inicio.
