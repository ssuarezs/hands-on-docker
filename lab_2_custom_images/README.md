# 🧪 Docker Lab 2: Imágenes Personalizadas con Dockerfile

## 🎯 Objetivo del laboratorio

Aprender a construir imágenes personalizadas mediante el uso de un `Dockerfile`, comprender la estructura y los comandos básicos para definir imágenes, y ejecutar contenedores a partir de dichas imágenes. Todo enfocado al ecosistema .NET para mantener familiaridad con el stack del equipo.

> **Nota:** En este laboratorio trabajaremos con una imagen base de .NET SDK, personalizándola con archivos y configuraciones propias usando un Dockerfile. Veremos dos enfoques: manual (línea de comandos) y asistido (Visual Studio).

---

## 🧰 Requisitos previos

* Haber completado el Laboratorio 1
* Tener Docker Desktop funcionando correctamente
* Tener instalado Visual Studio 2022 o superior con el workload de desarrollo .NET
* Conocimientos básicos de terminal / línea de comandos

---

# ⚙️ Parte 0: Crear una imagen sencilla personalizada con Nginx

## 🎯 Objetivo

Construir una imagen de Docker personalizada basada en `nginx` que sirva un archivo HTML estático con un mensaje propio.

---

## 🛠️ Paso 1: Preparar la estructura de archivos

```bash
mkdir lab2-nginx-basic
cd lab2-nginx-basic
mkdir html
```

Dentro de la carpeta `html`, crea un archivo `index.html` con el siguiente contenido:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Bienvenidos</title>
</head>
<body>
    <h1>Hola, trabajadores de FYM Technology, somos el equipo de desarrollo.</h1>
</body>
</html>
```

---

## 📄 Paso 2: Crear el Dockerfile

En la carpeta `lab2-nginx-basic`, crea un archivo llamado `Dockerfile` con el siguiente contenido:

```Dockerfile
FROM nginx:alpine
COPY html /usr/share/nginx/html
```

> 📝 Este `Dockerfile` usa una imagen base liviana de `nginx`, y copia nuestro archivo HTML en la ruta por defecto que `nginx` expone como servidor web.

---

## 🧱 Paso 3: Construir la imagen

```bash
docker build -t custom-nginx:1.0 .
```

> `custom-nginx:1.0` será el nombre de la imagen personalizada que estamos creando.

---

## 🐳 Paso 4: Ejecutar el contenedor

```bash
docker run --rm -p 8080:80 custom-nginx:1.0
```

* Abre tu navegador y ve a `http://localhost:8080`
* Deberías ver el mensaje personalizado en pantalla.

---

## 📘 Explicación del Dockerfile básico

1. **FROM nginx\:alpine**: usa como base una imagen liviana de nginx.
2. **COPY html /usr/share/nginx/html**: reemplaza los archivos HTML por defecto con los nuestros personalizados.

> Este es un excelente primer ejemplo de cómo Docker permite empaquetar y desplegar contenido estático de manera ultra rápida y portable.

---

# ⚙️ Parte 1: Crear una imagen personalizada por línea de comandos

## 🧾 Paso 1: ¿Qué es una imagen personalizada?

Una imagen personalizada es una versión modificada de una imagen base que incluye tu propio código, librerías o configuraciones específicas. Se construye a partir de un **Dockerfile**, que es un archivo de texto que describe las instrucciones paso a paso para construir esa imagen.

> 📘 Las imágenes son la base de los contenedores. Al ejecutar un contenedor, Docker parte de una imagen y le agrega una capa de escritura temporal.

---

## 🛠️ Paso 2: Crear el proyecto base en .NET

```bash
# Crear una carpeta de trabajo
mkdir lab2-dockerfile
cd lab2-dockerfile

# Crear un proyecto de API minimalista en .NET
dotnet new webapi -n DemoApi
cd DemoApi
```

> Este comando crea una API REST básica con controladores mínimos (Minimal API). El proyecto expone un endpoint por defecto y puede probarse con Swagger.

---

## 📄 Paso 3: Crear el Dockerfile manualmente

Dentro de la carpeta `DemoApi`, crea un archivo llamado `Dockerfile` (sin extensión):

```Dockerfile
# Etapa 1: Construcción del proyecto
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /app
COPY . ./
RUN dotnet publish -c Release -o out

# Etapa 2: Imagen final optimizada
FROM mcr.microsoft.com/dotnet/aspnet:7.0
WORKDIR /app
COPY --from=build /app/out .
ENTRYPOINT ["dotnet", "DemoApi.dll"]
```

> 🔍 Usamos `aspnet:7.0` en la imagen final para que tenga el entorno de ejecución necesario para aplicaciones web.

---

## 📘 Explicación rápida del Dockerfile

El archivo `Dockerfile` que creaste contiene una serie de instrucciones que le indican a Docker cómo construir la imagen de tu aplicación:

1. **FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build**: establece una imagen base con las herramientas necesarias para compilar proyectos .NET. Esta es la primera etapa (build).
2. **WORKDIR /app**: define el directorio de trabajo dentro del contenedor.
3. **COPY . ./**: copia el contenido del proyecto local al contenedor.
4. **RUN dotnet publish -c Release -o out**: compila el proyecto y deja los archivos en la carpeta `out`.
5. **FROM mcr.microsoft.com/dotnet/aspnet:7.0**: define una nueva imagen base más ligera para ejecutar la app (runtime), sin el SDK.
6. **WORKDIR /app**: nuevamente define el directorio de trabajo.
7. **COPY --from=build /app/out .**: copia los archivos ya compilados desde la etapa de build.
8. **ENTRYPOINT \["dotnet", "DemoApi.dll"]**: define el comando que se ejecuta al iniciar el contenedor.

> Este patrón de dos etapas se llama **multi-stage build**, y permite generar imágenes más pequeñas y eficientes.

---

## 🧱 Paso 4: Construir la imagen personalizada

```bash
docker build -t demoapi:1.0 .
```

* `docker build` lee el Dockerfile en el directorio actual y crea una imagen.
* `-t demoapi:1.0` le asigna un nombre y etiqueta (`tag`).

---

## 🐳 Paso 5: Ejecutar el contenedor a partir de la imagen

```bash
docker run --rm -p 5000:80 demoapi:1.0
```

* `--rm` borra el contenedor al finalizar la ejecución.
* `-p 5000:80` mapea el puerto 80 del contenedor al 5000 de tu máquina.
* Abre tu navegador en `http://localhost:5000/swagger` para ver la API funcionando.

---

## 🔍 Paso 6: Inspeccionar la imagen construida

```bash
docker images
```

> Lista todas las imágenes locales. Verifica que `demoapi:1.0` esté presente.

```bash
docker history demoapi:1.0
```

> Muestra las capas creadas por el Dockerfile, lo cual ayuda a depurar o analizar la construcción.

---

# 🧪 Parte 2: Crear imagen personalizada con Visual Studio

## 🎯 Objetivo

Aprovechar la integración de Docker en Visual Studio para generar automáticamente el Dockerfile y construir imágenes de forma asistida.

---

## 🛠️ Paso 1: Crear un nuevo proyecto en Visual Studio

1. Abrir Visual Studio > Crear nuevo proyecto > **API de ASP.NET Core**
2. Asigna nombre: `DemoApiVS`
3. Finaliza la creación del proyecto sin activar Docker aún

---

## ➕ Paso 2: Agregar compatibilidad con contenedores Docker

1. Haz clic derecho sobre el proyecto en el Explorador de soluciones
2. Selecciona **Agregar > Compatibilidad con contenedores Docker**
3. Elige **Linux** como sistema operativo objetivo

---

## 📄 Paso 3: Explora el Dockerfile generado automáticamente

Visual Studio generará un `Dockerfile` en la raíz del proyecto. Puedes revisarlo y modificarlo si deseas optimizar la construcción o cambiar la versión de base.

---

## 🧱 Paso 4: Construir y ejecutar desde Visual Studio

1. Cambia la configuración de ejecución a `Docker` en la barra superior.
2. Haz clic en **Start (▶️)** para compilar la imagen y lanzar el contenedor.
3. Visual Studio mostrará la salida directamente en la consola integrada.

> 🧠 Este enfoque es ideal para desarrolladores acostumbrados a Visual Studio, ya que automatiza varios pasos, aunque puede ser menos flexible que el método manual.

---

## 🧠 Preguntas para reflexión

* ¿Qué diferencia hay entre la imagen base (`sdk`) y la imagen final (`aspnet`)?
* ¿Qué ventajas trae separar la etapa de compilación y ejecución en distintas capas?
* ¿Qué beneficios ofrece `ENTRYPOINT` frente a `CMD`?
* ¿Qué pasa si modificas el código fuente y no vuelves a ejecutar `docker build`?
* ¿Cuál enfoque prefieres: terminal o Visual Studio? ¿Por qué?

---

## 📦 Entregable esperado

Para este laboratorio se espera como entregable una estructura de carpetas organizada y subida en una rama específica del repositorio. A continuación, se describen los pasos a seguir:

### 📁 Estructura del proyecto

Cada ingeniero debe crear una carpeta con su nombre dentro del proyecto, y dentro de esta carpeta incluir tres subcarpetas correspondientes a las tres partes del laboratorio:

```
lab2_nombre_apellido/
├── parte0-nginx-basic/
│   ├── Dockerfile
│   └── html/
│       └── index.html
├── parte1-cli-net/
│   ├── DemoApi/
│   │   ├── Program.cs
│   │   ├── Dockerfile
│   │   └── ...
├── parte2-vs-net/
│   └── DemoApiVS/
│       ├── Dockerfile
│       └── ...
```

> 🔁 Asegúrate de que cada carpeta esté limpia y funcional, y que el contenedor pueda ser reconstruido a partir del Dockerfile proporcionado.

### 🌿 Rama en Git

La entrega debe hacerse en una rama con la siguiente nomenclatura:

```
lab2_nombre_apellido
```

Por ejemplo:

```
lab2_santiago_suarez
```

Sube la estructura completa del laboratorio en esa rama para su revisión.

---

## ✅ Cierre del laboratorio

Ahora sabes cómo crear imágenes personalizadas a partir de código fuente en .NET tanto desde la terminal como desde Visual Studio, aplicando buenas prácticas para aplicaciones web. Esto sienta la base para integrar Docker en tu ciclo de desarrollo habitual, con opciones para automatización o asistencia gráfica.

En el próximo laboratorio, aprenderemos sobre redes, volúmenes y cómo conectar múltiples contenedores entre sí usando `docker-compose`.
