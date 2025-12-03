# Ingeniería de Requisitos: 

## 🎯 Análisis del Sistema

### 1. Descripción General
SmartTask es un gestor de tareas inteligente que utiliza IA para priorizar automáticamente las tareas del usuario basándose en urgencia, impacto y niveles de energía. 
***Daryll tienes que explicarme mejor como piensas implementar la IA para poder hacer mejor los casos de uso.

### 2. Stakeholders Identificados
- **Usuarios finales**: Personas que gestionan tareas diarias
- **Desarrolladores**: Equipo de desarrollo y mantenimiento

### Roles de usuarios finales 
- Usuario normal: Personas que gestionan su tareas diarias solamente... por ahora 

---

## 📋 Requisitos Funcionales

### Módulo 1: Gestión de Usuarios

#### RF-001: Registro de Usuario
- **Descripción**: El sistema debe permitir el registro de nuevos usuarios a través de un usuario y contraseña 
- **Criterios de Aceptación**:
  - Campos requeridos: email, contraseña, nombre
  - Validación de formato de email
  - Contraseña con mínimo 8 caracteres (validaciones en el front para que contenga simbolos y números para mayor seguridad) La validación de la contraseña en el front.
   - Verificación de unicidad de email (Que no se repita en la base de datos )
   - **Prioridad**: Alta. 

#### RF-002: Autenticación de Usuario
- **Descripción**: El sistema debe permitir el inicio de sesión de usuarios registrados
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Login con email y contraseña
  - Manejo de sesiones seguras (Utilizar JWT)
  - Cierre de sesión

### Módulo 2: Gestión de Tareas

#### RF-003: Creación de Tareas
- **Descripción**: El sistema debe permitir a un usuario crear nuevas tareas
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Campos obligatorios: título, descripción
  - Campos opcionales: fecha límite, categoría, tiempo estimado, importancia
  - Asignación automática de prioridad por IA
  - Clasificación en urgencia/impacto ***(La urgencia puede ser de bajo, medio ,alto y el impacto igual)

#### RF-004: Edición de Tareas
- **Descripción**: El sistema debe permitir modificar tareas existentes tanto total como parcialmente
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Modificación parcial o total de los campos
  - Recalcular prioridad automáticamente al modificar  ***(IA)
  - Historial de cambios 

#### RF-005: Eliminación de Tareas
- **Descripción**: El sistema debe permitir eliminar tareas. Se debe eliminar individualmente
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Eliminación individual
  - Confirmación antes de eliminar
  - Opción de archivar en lugar de eliminar

#### RF-006: Listado de Tareas
- **Descripción**: El sistema debe mostrar listas de tareas organizadas. Este listado debe tener un filtrado inicial por fecha de creación mostrando en primer lugar las últimas que han sido creadas.
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Visualización por prioridad (colores)
  - Filtros por categoría, estado, fecha
  - Búsqueda por texto. Una barra en la que al escribir el nombre de la tarea vayan apareciendo las tareas que tengan un nombre parecido al escrito.
  - Ordenamiento múltiple

### Módulo 3: Sistema de Priorización con IA

#### RF-007: Clasificación de Prioridades
- **Descripción**: El sistema debe clasificar tareas en alta/media/baja prioridad
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Uso de algoritmo de árbol de decisión (Scikit-Learn)
  - Considerar: plazo, importancia, tiempo estimado, categoría
  - Asignación automática al crear/editar tareas
  - Visualización con código de colores

#### RF-008: Predicción de Completado
- **Descripción**: El sistema debe predecir probabilidad de completar tareas a tiempo
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Uso de red neuronal (PyTorch)
  - Basado en historial del usuario
  - Mostrar porcentaje de probabilidad
  - Actualizar predicciones periódicamente

#### RF-009: Recomendación del Día
- **Descripción**: El sistema debe sugerir una tarea prioritaria diaria
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Algoritmo que combine prioridad y contexto
  - Mostrar en dashboard principal
  - Explicación de por qué fue seleccionada
  - Opción de aceptar o posponer

### Módulo 4: Gestión de Energía y Contexto

#### RF-010: Registro de Nivel de Energía
- **Descripción**: El sistema debe permitir registrar niveles de energía del usuario. Al terminar cada tarea o según la intensidad de la tarea se debería realzar esta pregunta.
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Escala simple (alta/media/baja)
  - Registro diario opcional
  - Influencia en recomendaciones

#### RF-011: Ajuste de Prioridades por Energía
- **Descripción**: El sistema debe ajustar recomendaciones según nivel de energía  ***(IA)
- **Prioridad**: Baja
- **Criterios de Aceptación**:
  - Tareas que requieren menos energía en momentos de baja energía
  - Adaptación dinámica de sugerencias

### Módulo 5: Dashboard y Visualización

#### RF-012: Dashboard Principal
- **Descripción**: El sistema debe mostrar un dashboard con resumen de productividad. Este resumen debería ser mensual.
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Métricas clave: tareas completadas, pendientes, atrasadas
  - Gráfico de distribución de prioridades
  - Recomendación del día destacada
  - Tendencias de productividad


### Módulo 6: Herramientas 

#### RF-013: Seleccionar herramienta 
- **Descripción**: El sistema debe permitir al usuario seleccionar la herramienta que sea necesaria para el mismo.
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Mostrar herramientas 
  - Se cargan correctamente las herramientas 

#### RF-014: Resumir texto
- **Descripción**: El sistema debe permitir al usuario introducir texto y devolver un resumen del mismo con las palabras claves del mismo.
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Texto de menos de 5000 mil caracteres y sin comillas dobles.

#### RF-015: Analizar  
- **Descripción**: El sistema debe permitir al usuario introducir una imagen en la sección de análisis de diagramas.
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Imagen en formato png o jpg.



