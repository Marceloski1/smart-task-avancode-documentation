## 🧩 Módulo 1: Gestión de Usuarios

### **UC-01 – Registro de Usuario**

**Actor principal:** Usuario  
**Objetivo:** Crear una cuenta nueva en el sistema.

**Precondición:** No debe existir una cuenta con el mismo correo.

**Flujo principal:**

1. El usuario selecciona **“Registrarse”**.
2. Ingresa email, nombre y contraseña.
3. El sistema valida formato y unicidad del email.
4. El sistema registra al nuevo usuario.
5. Muestra mensaje de confirmación.

**Postcondición:** Usuario registrado con estado activo.

**Flujos alternativos:**
- (A1) Email existente → se muestra error.    
- (A2) Contraseña inválida → mensaje de requisitos mínimos.

---
### **UC-02 – Autenticación de Usuario**

**Actor principal:** Usuario  
**Objetivo:** Iniciar sesión de forma segura.

**Precondición:** Usuario debe estar registrado.

**Flujo principal:**

1. El usuario ingresa email y contraseña.
2. El sistema valida las credenciales.
3. Si son válidas, genera token **JWT**.
4. Redirige al dashboard principal.

**Flujos alternativos:**

- (A1) Credenciales incorrectas → se muestra error.
- (A2) Usuario inactivo → mensaje de suspensión.

___
## 📝 Módulo 2: Gestión de Tareas

### **UC-03 – Crear Tarea**

**Actor principal:** Usuario  
**Objetivo:** Crear una nueva tarea.

**Precondición:** Usuario autenticado.

**Flujo principal:**

1. Usuario selecciona **“Nueva Tarea”**.
2. Ingresa título, descripción y atributos (urgencia, impacto, etc.).
3. El sistema envía los datos al módulo de IA.
4. La IA devuelve `priority_score` y `priority_level`.    
5. El sistema guarda la tarea con la prioridad asignada.

**Postcondición:** Nueva tarea creada.

**Flujos alternativos:**

- (A1) Falla en IA → se asigna prioridad por defecto (“media”).

---

### **UC-04 – Editar Tarea**

**Actor principal:** Usuario  
**Objetivo:** Modificar tarea existente.

**Precondición:** Tarea existente y editable.

**Flujo principal:**

1. Usuario selecciona una tarea para editar.    
2. Modifica los campos deseados.    
3. El sistema recalcula la prioridad automáticamente (IA).    
4. Guarda los cambios.    

**Postcondición:** Tarea actualizada con nueva prioridad.

**Flujos alternativos:**

- (A1) Usuario cancela edición → no se guardan cambios.  

---

### **UC-05 – Eliminar Tarea**

**Actor principal:** Usuario  
**Objetivo:** Eliminar o archivar una tarea.

**Flujo principal:**

1. Usuario selecciona la tarea.   
2. Solicita eliminarla.   
3. El sistema pide confirmación.   
4. El usuario confirma o decide archivarla.   

**Postcondición:** Tarea eliminada o archivada según la acción.

---

### **UC-06 – Listar y Filtrar Tareas**

**Actor principal:** Usuario  
**Objetivo:** Visualizar tareas filtradas u ordenadas según diferentes criterios.

**Flujo principal:**

1. Usuario accede al listado de tareas.    
2. El sistema aplica filtros y ordenamientos. 
3. Muestra las tareas con colores según prioridad.
  

**Postcondición:** Tareas visibles y organizadas para el usuario.

---

## 🤖 Módulo 3: Sistema de Priorización con IA

### **UC-07 – Clasificar Prioridad (IA)**

**Actor principal:** Sistema (IA)  
**Objetivo:** Calcular el nivel de prioridad de una tarea.

**Disparador:** Creación o edición de tarea.

**Flujo principal:**

1. El sistema recibe los parámetros de la tarea.   
2. Aplica el modelo de **árbol de decisión (Scikit-Learn)**.    
3. Devuelve la prioridad calculada (`priority_score`, `priority_level`).
  
**Postcondición:** La tarea almacena la prioridad en la base de datos.

---

### **UC-08 – Predecir Probabilidad de Completado**

**Actor principal:** Sistema (IA)  
**Objetivo:** Estimar la probabilidad de que una tarea se complete a tiempo.

**Flujo principal:**

1. El sistema recopila datos históricos del usuario (`tasks`, `energy_logs`).
2. Aplica el modelo de **red neuronal (PyTorch)**.
3. Calcula y guarda `completion_probability`.

**Postcondición:** La probabilidad es visible en el dashboard o detalles de tarea.

---

### **UC-09 – Generar Recomendación del Día**

**Actor principal:** Sistema (IA)  
**Objetivo:** Sugerir la tarea más relevante para el día.

**Flujo principal:**

1. El sistema analiza tareas pendientes, energía actual y fechas límite.
2. Combina los resultados de los modelos de prioridad y completado.
3. Calcula `confidence_score` y selecciona una tarea óptima.
4. Muestra la recomendación en el dashboard.
5. El usuario puede aceptar, rechazar o posponer.

**Postcondición:** Registro generado en `daily_recommendations`.

---

## ⚡ Módulo 4: Energía y Contexto

### **UC-10 – Registrar Nivel de Energía**

**Actor principal:** Usuario  
**Objetivo:** Registrar su nivel de energía actual.

**Flujo principal:**

1. Al finalizar una tarea o desde el panel, el sistema pregunta el nivel de energía.
2. El usuario selecciona **alta / media / baja**.
3. El sistema guarda el registro en `energy_logs`.

**Postcondición:** Nivel de energía registrado para análisis futuro.

---

### **UC-11 – Ajustar Prioridades según Energía**

**Actor principal:** Sistema (IA)  
**Objetivo:** Reordenar las tareas recomendadas según el nivel de energía actual.

**Flujo principal:**

1. El sistema detecta energía baja.
2. Filtra tareas que requieren menos esfuerzo.
3. Actualiza la lista de sugerencias en el dashboard.

**Postcondición:** Recomendaciones adaptadas dinámicamente.

---

## 📊 Módulo 5: Dashboard

### **UC-12 – Mostrar Dashboard**

**Actor principal:** Usuario  
**Objetivo:** Visualizar métricas de productividad y progreso.

**Flujo principal:**

1. El usuario accede al dashboard.
2. El sistema muestra:
    - Tareas completadas, pendientes y atrasadas.
    - Gráfico de distribución de prioridades.
    - Recomendación del día.
3. El usuario puede explorar tareas o generar reportes.    

**Postcondición:** Información de productividad visible.

---

## ⚙️Módulo 6: Herramientas 

### Submodulo Resumidor: 

#### **UC-13 – Resumir Texto**

Actor principal:Usuario 
Objetivo: Resumir textos de difícil comprensión o de gran extensión. 

**Flujo principal:** 

1. El usuario accede a herramientas, concretamente a la sección de resumen.
2. El sistema muestra un recuadro donde el usuario puede introducir el texto que se desea resumir.
3. El usuario introduce el texto que desea resumir y acciona el botón de resumir. 
4. Se muestra una pantalla de carga (se está realizando la petición a la api pipeline resumidor)
5. Se devuelve el texto resumido y se le muestra al usuario el texto resumido y las palabras clave del mismo. 

Postcondición: El usuario puede visualizar el resumen del texto y las palabras claves del mismo.

### Submodulo Analizador de diagrama: 

#### **UC-14 – Análisis de diagrama**

Actor principal:Usuario 
Objetivo: Analizar diagramas de díficil comprensión 

**Flujo principal:** 

1. El usuario accede a herramientas, concretamente a la sección de análisis de diagramas.
2. El sistema muestra un recuadro donde el usuario puede introducir la foto del diagrama se desea se desea analizar.
3. Se carga la foto en la app. 
4. El usuario introduce la foto de diagrama que desea analizar y acciona el botón de analizar.
5.  Se muestra una pantalla de carga (se está realizando la petición a la api ......)
6. Se devuelve el análisis del diagrama. 

Postcondición: El usuario puede visualizar el análisis realizado.



### Diagrama de casos de uso del sistema: 

``` plantuml
@startuml
title 📘 SmartTask - Casos de Uso del Sistema

left to right direction
actor "Usuario" as User
actor "Sistema de IA" as AI

package "Módulo 1: Gestión de Usuarios" {
    usecase "UC-01 Registro de Usuario" as UC01
    usecase "UC-02 Autenticación de Usuario" as UC02
}

package "Módulo 2: Gestión de Tareas" {
    usecase "UC-04 Crear Tarea" as UC04
    usecase "UC-05 Editar Tarea" as UC05
    usecase "UC-06 Eliminar Tarea" as UC06
    usecase "UC-07 Listar y Filtrar Tareas" as UC07
}

package "Módulo 3: Sistema de Priorización con IA" {
    usecase "UC-08 Clasificar Prioridad (IA)" as UC08
    usecase "UC-09 Predecir Probabilidad de Completado" as UC09
    usecase "UC-10 Generar Recomendación del Día" as UC10
}

package "Módulo 4: Energía y Contexto" {
    usecase "UC-11 Registrar Nivel de Energía" as UC11
    usecase "UC-12 Ajustar Prioridades según Energía" as UC12
}

package "Módulo 5: Dashboard y Reportes" {
    usecase "UC-13 Mostrar Dashboard" as UC13
    usecase "UC-03 Generar Reporte de Productividad" as UC03
}

' --- Relaciones con actores ---
User --> UC01
User --> UC02
User --> UC04
User --> UC05
User --> UC06
User --> UC07
User --> UC11
User --> UC13
User --> UC03

AI --> UC08
AI --> UC09
AI --> UC10
AI --> UC12

' --- Relaciones entre casos de uso ---
UC04 --> UC08 : <<include>>
UC05 --> UC08 : <<include>>
UC08 --> UC09 : <<include>>
UC09 --> UC10 : <<include>>
UC11 --> UC12 : <<include>>
UC12 --> UC10 : <<extend>>
@enduml
```