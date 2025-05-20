# 🧠 Reflexión – Laboratorio 2: Imágenes Personalizadas con Docker

## 1. ¿Qué diferencia hay entre la imagen base (`sdk`) y la imagen final (`aspnet`)?

* La imagen **`sdk`** contiene herramientas para compilar y publicar aplicaciones (`dotnet build`, `dotnet publish`).
* La imagen **`aspnet`** solo tiene el entorno necesario para ejecutar una aplicación ASP.NET Core.
* Usar ambas permite compilar en un entorno completo (`sdk`) y ejecutar en un entorno más ligero y seguro (`aspnet`), gracias al patrón **multi-stage build**.

---

## 2. ¿Qué ventajas trae separar la etapa de compilación y ejecución en distintas capas?

* ✅ **Tamaño reducido**: la imagen final no arrastra herramientas de desarrollo.
* ✅ **Seguridad**: al tener menos herramientas disponibles, hay menos superficie de ataque.
* ✅ **Eficiencia en el caché**: Docker reutiliza capas previas si no han cambiado.
* ✅ **Responsabilidad única**: cada imagen tiene un propósito específico (compilar vs. ejecutar).

---

## 3. ¿Qué beneficios ofrece `ENTRYPOINT` frente a `CMD`?

* `ENTRYPOINT`: define el **comando que siempre se ejecuta** cuando el contenedor arranca.
* `CMD`: define **argumentos por defecto** que pueden sobrescribirse en `docker run`.

Ejemplo combinado:

```Dockerfile
ENTRYPOINT ["dotnet"]
CMD ["DemoApi.dll"]
```

Esto ejecuta `dotnet DemoApi.dll`, pero permite cambiarlo por ejemplo a `dotnet OtraApp.dll` al ejecutar.

---

## 4. ¿Qué pasa si modificas el código fuente y no vuelves a ejecutar `docker build`?

* El contenedor usará la **versión vieja** de la imagen sin tus cambios.
* Docker **no detecta automáticamente** los cambios en el código fuente.
* Siempre debes **reconstruir la imagen** con `docker build` para aplicar los cambios.

---

## 5. ¿Cuál enfoque prefieres: terminal o Visual Studio? ¿Por qué?

### 🖥 Terminal (CLI)

* Mayor **control y transparencia**
* Ideal para automatización, CI/CD y entornos Linux
* Requiere más conocimiento técnico

### 🧩 Visual Studio

* Integración rápida y fácil para desarrolladores .NET
* Automatiza muchos pasos
* Menos flexible en algunos contextos productivos

> 🔁 **Lo ideal**: dominar ambos enfoques según el contexto del proyecto o del equipo.
