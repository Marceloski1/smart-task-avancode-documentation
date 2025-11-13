# Ingeniería de Requisitos: 

## 🎯 Análisis del Sistema

### 1. Descripción General
SmartTask es un gestor de tareas inteligente que utiliza IA para priorizar automáticamente las tareas del usuario basándose en urgencia, impacto y niveles de energía. 
***Daryll tienes que explicarme mejor como piensas implementar la IA para poder hacer mejor los casos de uso.

### 2. Stakeholders Identificados
- **Usuarios finales**: Personas que gestionan tareas diarias
- **Administradores del sistema**: Mantenimiento de la plataforma
- **Desarrolladores**: Equipo de desarrollo y mantenimiento

### Roles de usuarios finales 
- Usuario normal: Personas que gestionan su tareas diarias solamente... por ahora 
- Admin: Se encarga de gestionar los useCases más cruciales del negocio con respecto a la gestión dentro de la app.

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
  - Recuperación de contraseña  🤓🤓🤓(Implementación en el back a decidir con que gestor de emails)
  - Cierre de sesión

#### RF-003: Perfil de Usuario
- **Descripción**: El sistema debe permitir gestionar el perfil del usuario
- **Prioridad**: Baja
- **Criterios de Aceptación**:
  - Edición de información personal  ***( Se debe decidir posteriormente que información personal debe poder agregar el usuario al sistema)
  - Configuración de preferencias ***(Se debe definir las preferencias a utilizar)
  - Gestión de niveles de energía típicos

### Módulo 2: Gestión de Tareas

#### RF-004: Creación de Tareas
- **Descripción**: El sistema debe permitir a un usuario crear nuevas tareas
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Campos obligatorios: título, descripción
  - Campos opcionales: fecha límite, categoría, tiempo estimado, importancia
  - Asignación automática de prioridad por IA
  - Clasificación en urgencia/impacto ***(La urgencia puede ser de bajo, medio ,alto y el impacto igual)

#### RF-005: Edición de Tareas
- **Descripción**: El sistema debe permitir modificar tareas existentes tanto total como parcialmente
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Modificación parcial o total de los campos
  - Recalcular prioridad automáticamente al modificar  ***(IA)
  - Historial de cambios 

#### RF-006: Eliminación de Tareas
- **Descripción**: El sistema debe permitir eliminar tareas. Se debe eliminar individualmente
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Eliminación individual
  - Confirmación antes de eliminar
  - Opción de archivar en lugar de eliminar

#### RF-007: Listado de Tareas
- **Descripción**: El sistema debe mostrar listas de tareas organizadas. Este listado debe tener un filtrado inicial por fecha de creación mostrando en primer lugar las últimas que han sido creadas.
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Visualización por prioridad (colores)
  - Filtros por categoría, estado, fecha
  - Búsqueda por texto. Una barra en la que al escribir el nombre de la tarea vayan apareciendo las tareas que tengan un nombre parecido al escrito.
  - Ordenamiento múltiple

### Módulo 3: Sistema de Priorización con IA

#### RF-008: Clasificación de Prioridades
- **Descripción**: El sistema debe clasificar tareas en alta/media/baja prioridad
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Uso de algoritmo de árbol de decisión (Scikit-Learn)
  - Considerar: plazo, importancia, tiempo estimado, categoría
  - Asignación automática al crear/editar tareas
  - Visualización con código de colores

#### RF-009: Predicción de Completado
- **Descripción**: El sistema debe predecir probabilidad de completar tareas a tiempo
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Uso de red neuronal (PyTorch)
  - Basado en historial del usuario
  - Mostrar porcentaje de probabilidad
  - Actualizar predicciones periódicamente

#### RF-010: Recomendación del Día
- **Descripción**: El sistema debe sugerir una tarea prioritaria diaria
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Algoritmo que combine prioridad y contexto
  - Mostrar en dashboard principal
  - Explicación de por qué fue seleccionada
  - Opción de aceptar o posponer

### Módulo 4: Dashboard y Visualización

#### RF-011: Dashboard Principal
- **Descripción**: El sistema debe mostrar un dashboard con resumen de productividad. Este resumen debería ser mensual.
- **Prioridad**: Alta
- **Criterios de Aceptación**:
  - Métricas clave: tareas completadas, pendientes, atrasadas
  - Gráfico de distribución de prioridades
  - Recomendación del día destacada
  - Tendencias de productividad

#### RF-012: Reportes de Productividad
- **Descripción**: El sistema debe generar reportes de productividad. Definir formato (xls o pdf)
  - Exportación en formatos comunes

### Módulo 5: Gestión de Energía y Contexto ***Módulo a revisar entre todos

#### RF-013: Registro de Nivel de Energía
- **Descripción**: El sistema debe permitir registrar niveles de energía del usuario. Al terminar cada tarea o según la intensidad de la tarea se debería realzar esta pregunta.
- **Prioridad**: Media
- **Criterios de Aceptación**:
  - Escala simple (alta/media/baja)
  - Registro diario opcional
  - Influencia en recomendaciones

#### RF-014: Ajuste de Prioridades por Energía
- **Descripción**: El sistema debe ajustar recomendaciones según nivel de energía  ***(IA)
- **Prioridad**: Baja
- **Criterios de Aceptación**:
  - Tareas que requieren menos energía en momentos de baja energía
  - Adaptación dinámica de sugerencias

##  🤓 Ideas a tener en consideración para nuevas funcionalidades 
- El usuario puede crear sus propias categorías
- El sistema debe contar con un historial de cambios para el usuario (Sistema de trazas )
- En caso de decidir utilizar un sistma de trazas decidir la estrategia para guardar las trazas en la base de datos
- Las tareas deberían tener un estado para de que esta se desee archivar, posponer o .... 
- Para una mejor guía del algoritmo se puede agregar el parametro de credibilidad de terminalidad (es posible el objetivo planteado pese a la dificultad) En el RF-09 se explica un poco mejor este punto
- El RF-010: Recomendación del Día podría ocurrir si existe un aprendizaje del comportamiento de cada usuario
---

## 🔄 Flujos de Trabajo Principales

### Flujo 1: Creación y Priorización de Tarea
1. Usuario crea tarea con atributos
2. Sistema envía datos al módulo de IA
3. IA clasifica prioridad y calcula probabilidad de éxito
4. Sistema muestra tarea con prioridad asignada

### Flujo 2: Recomendación Diaria
1. Sistema analiza tareas pendientes
2. IA selecciona tarea óptima considerando múltiples factores
3. Sistema muestra recomendación en dashboard
4. Usuario puede aceptar o ignorar recomendación


