# 🧭 Modelo de Negocio – SmartTask

**Sistema:** SmartTask – Asistente Inteligente de Priorización de Tareas  
**Versión:** 1.0  
**Autores:** 
	- Daryll Lorenzo Alfonso - [https://github.com/DaryllLorenzo](https://github.com/DaryllLorenzo)|
	- Roberto José Martínez Barrios - [https://github.com/AzerXP](https://github.com/AzerXP)|
	- Eduardo Marcelo Mazzola Fernández - [https://github.com/Marceloski1](https://github.com/Marceloski1)|
	- Carlos Miguel Piedra Álvarez - [https://github.com/cStoneDev](https://github.com/cStoneDev)|
	- Andy Clemente Gago - [https://github.com/AndyCG03](https://github.com/AndyCG03)|
**Última actualización:** Diciembre 2025

---
## 🎯 1. Propósito del Sistema

**SmartTask** es una aplicación de productividad que ayuda a los usuarios a **gestionar y priorizar tareas** utilizando **inteligencia artificial (IA)**.  
El sistema analiza variables como **urgencia**, **impacto**, **energía disponible** y **patrones de comportamiento** del usuario para **sugerir tareas óptimas** en cada momento del día.
El sistema también cuenta con herramientas para facilitar la realización de las tareas como lo son el resumidor y un identificador de diagramas de casos de uso.

El objetivo principal es **maximizar la productividad y reducir la carga cognitiva** asociada con la planificación manual.

___

## 🧑‍💼 2. Actores del Negocio

| Actor             | Descripción                                                                               | Interacciones Principales                                                                    |
| ----------------- | ----------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **Usuario**       | Persona que utiliza el sistema para crear, gestionar y completar tareas.                  | Crear tareas, registrar energía, recibir recomendaciones, utilizar herramientas del sistema. |
| **Sistema de IA** | Módulo interno encargado de clasificar y predecir prioridades, completitud y sugerencias. | Clasificación, predicción, recomendaciones diarias.                                          |

_(El rol Admin se omite porque aún no realiza acciones directamente desde el front en esta versión del sistema pero se tiene en cuenta al momento de crear un usuario.)

## ⚙️ 3. Procesos Clave del Negocio

SmartTask se estructura en **cinco procesos principales** (módulos funcionales).  
A continuación se proporciona un enlace para la revisión de los casos de uso: [[Casos de Uso]]

## 🧩 4. Entidades del Negocio (Resumen conceptual)  

| Entidad                  | Descripción                                | Relaciones principales                                          |
| ------------------------ | ------------------------------------------ | --------------------------------------------------------------- |
| **Usuario**              | Representa a la persona que usa SmartTask. | Tiene muchas `tareas`, `niveles de energía`, `recomendaciones`. |
| **Tarea**                | Actividad gestionada por el usuario.       | Pertenece a un `usuario`, puede tener una `categoría`.          |
| **Categoría**            | Agrupación personalizada de tareas.        | Creada por un `usuario`.                                        |
| **Recomendación Diaria** | Tarea sugerida por la IA.                  | Relacionada con `usuario` y `tarea`.                            |
| **Registro de Energía**  | Estado energético del usuario.             | Afecta las recomendaciones.                                     |


# 5. Reglas del Negocio 

## 🧩 **5.1 Reglas de Acceso y Seguridad**

| Código | Regla                                         | Descripción                                                                                                                            |
| ------ | --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| RN-01  | **Acceso restringido a tareas propias**       | Cada usuario solo puede visualizar, editar o eliminar sus propias tareas. El backend valida el `user_id` antes de cada operación CRUD. |
| RN-02  | **Autenticación obligatoria**                 | Todo endpoint (excepto `/auth/register` y `/auth/login`) requiere un token JWT.                                                        |
| RN-03  | **Sesiones seguras**                          | Si se usa cookie de sesión, debe ser `HttpOnly`, `Secure` y con `SameSite=Strict`.                                                     |
| RN-04  | **Cierre de sesión forzado tras inactividad** | Si un usuario no interactúa por más de 24 horas, la sesión se invalida automáticamente (token expira).                                 |

--- 
## ⚙️ **5.2 Reglas de Gestión de Tareas**

| Código | Regla                             | Descripción                                                                                                                            |
| ------ | --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| RN-06  | **Propiedad de tarea**            | Toda tarea debe tener un `user_id` asignado al momento de su creación.                                                                 |
| RN-07  | **Priorización automática**       | Cada vez que se crea o edita una tarea, el sistema recalcula su prioridad con base en: urgencia, impacto, energía actual y contexto.   |
| RN-08  | **Estados válidos de tarea**      | Solo se permiten los siguientes estados: `pendiente`, `en_progreso`, `completada`, `cancelada`.                                        |
| RN-09  | **Límite de tareas activas**      | Se recomienda un máximo de 50 tareas activas por usuario para evitar sobrecarga cognitiva. (Puede ajustarse por nivel de suscripción). |
| RN-10  | **Tareas completadas bloqueadas** | Una tarea marcada como completada no puede modificarse, solo visualizarse o eliminarse.                                                |

---

## 🤖 **5.3 Reglas del Sistema de IA**

| Código | Regla                          | Descripción                                                                                                                     |
| ------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| RN-11  | **Reentrenamiento periódico**  | La IA se reentrena cada 7 días con el historial de tareas completadas y feedback de usuario.                                    |
| RN-12  | **Clasificación transparente** | El sistema debe registrar la puntuación de prioridad (`priority_score`) y los factores usados para cada cálculo.                |
| RN-13  | **Predicción de completado**   | Para cada tarea nueva, la IA estima una probabilidad de éxito (`completion_probability`) entre 0 y 1.                           |
| RN-14  | **Validación del modelo IA**   | Antes de desplegar un modelo actualizado, debe validarse que su precisión sea mayor o igual al 60 %.                            |
| RN-15  | **Adaptación al usuario**      | El modelo ajusta su comportamiento individualmente según hábitos y patrones del usuario para realizar una recomendación diaria. |


---

## 💡 **5.4 Reglas sobre Recomendaciones Diarias**

| Código | Regla                                   | Descripción                                                                                                                         |
| ------ | --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| RN-16  | **Una recomendación por día y usuario** | Solo se permite generar una recomendación diaria activa por usuario. Si el usuario la rechaza, se puede regenerar solo manualmente. |
| RN-17  | **Basada en contexto actual**           | La recomendación se genera considerando: nivel de energía, tareas pendientes, urgencia y patrones previos.                          |
| RN-18  | **Feedback del usuario**                | El usuario puede marcar si la recomendación fue útil; este feedback se almacena y retroalimenta al modelo IA.                       |

---

## ⚡ **5.5 Reglas sobre Niveles de Energía y Contexto**

| Código | Regla                           | Descripción                                                                                                                               |
| ------ | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| RN-19  | **Registro diario obligatorio** | El usuario puede registrar su nivel de energía (bajo, medio, alto) una vez por día.                                                       |
| RN-20  | **Impacto en priorización**     | El nivel de energía afecta el peso de la urgencia e impacto en el cálculo de prioridad. Ejemplo: energía baja → priorizar tareas ligeras. |

---

## 📊 **5.6 Reglas sobre Dashboard**

| Código | Regla                        | Descripción                                                                   |
| ------ | ---------------------------- | ----------------------------------------------------------------------------- |
| RN-21  | **Dashboard en tiempo real** | Las métricas se actualizan cada vez que el usuario crea o completa una tarea. |

___
## ⚙️ **5.7 Reglas sobre Herramientas

| Código | Regla                       | Descripción                                                                                                            |
| ------ | --------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| RN-22  | **Set de herramientas**     | El usuario despliega un set de herramientas en su barra la lateral  con las herramientas disponibles en la aplicación. |
| RN-23  | **Resumidor**               | El usuario puede resumir textos de gran tamaño o de difícil comprensión y obtener las palabras claves del mismo.       |
| RN-24  | ***Análisis de diagramas*** | El usuario puede analizar diagramas de caso de uso y obtener información relevante de los mismos.                      |

