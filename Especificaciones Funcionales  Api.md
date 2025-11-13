
## 🧩 Estructura General

**Base URL:**

```
https://api.smarttask.com/v1/
```

**Formato:**

- Protocolo: HTTPS
    
- Formato de datos: JSON
    
- Autenticación: JWT (Bearer Token)
    

**Códigos de estado estándar:**

|Código|Significado|
|---|---|
|200|OK|
|201|Creado correctamente|
|400|Petición inválida|
|401|No autorizado|
|404|No encontrado|
|409|Conflicto (duplicado, etc.)|
|500|Error interno|

---

## 👤 1️⃣  Módulo de Gestión de Usuarios

### **UC-01 Registro de Usuario**

**POST /users/register**

**Request:**

```json
{
  "name": "Juan Pérez",
  "email": "juan@example.com",
  "password": "12345678"
}
```

**Response:**

```json
{
  "id": "usr_001",
  "name": "Juan Pérez",
  "email": "juan@example.com",
  "createdAt": "2025-11-12T10:00:00Z"
}
```

---

### **UC-02 Autenticación de Usuario**

**POST /auth/login**

**Request:**

```json
{
  "email": "juan@example.com",
  "password": "12345678"
}
```

**Response:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "usr_001",
    "name": "Juan Pérez",
    "email": "juan@example.com"
  }
}
```

---

## ✅  2️⃣Módulo de Gestión de Tareas

### **UC-04 Crear Tarea**

**POST /tasks**

**Request:**

```json
{
  "title": "Revisar informe semanal",
  "description": "Analizar resultados de ventas",
  "dueDate": "2025-11-15",
  "energyRequired": "medium"
}
```

**Response:**

```json
{
  "id": "tsk_101",
  "title": "Revisar informe semanal",
  "priority": "medium",
  "predictedCompletion": 0.78,
  "dueDate": "2025-11-15",
  "createdAt": "2025-11-12T12:00:00Z"
}
```

---

### **UC-05 Editar Tarea**

**PUT /tasks/{id}**

**Request:**

```json
{
  "title": "Revisar informe mensual",
  "priority": "high"
}
```

**Response:**

```json
{
  "id": "tsk_101",
  "title": "Revisar informe mensual",
  "priority": "high",
  "updatedAt": "2025-11-12T13:00:00Z"
}
```

---

### **UC-06 Eliminar Tarea**

**DELETE /tasks/{id}**

**Response:**

```json
{
  "message": "Tarea eliminada correctamente"
}
```

---

### **UC-07 Listar y Filtrar Tareas**

**GET /tasks?status=active&priority=high**

**Response:**

```json
[
  {
    "id": "tsk_101",
    "title": "Revisar informe mensual",
    "priority": "high",
    "status": "pending"
  }
]
```

---

$$Comentar-con-jefe-de-IA-estos-modulos $$
## 🧠 3️⃣ Módulo de IA (Prioridades y Recomendaciones)

### **UC-08 Clasificar Prioridad (IA)**

**POST /ai/prioritize**

**Request:**

```json
{
  "title": "Revisar informe semanal",
  "description": "Analizar resultados de ventas"
}
```

**Response:**

```json
{
  "priority": "medium",
  "confidence": 0.86
}
```

---

### **UC-09 Predecir Probabilidad de Completado**

**POST /ai/predict**

**Request:**

```json
{
  "taskId": "tsk_101"
}
```

**Response:**

```json
{
  "taskId": "tsk_101",
  "predictedCompletion": 0.78
}
```

---

### **UC-10 Generar Recomendación del Día**

**GET /ai/recommendation**

**Response:**

```json
{
  "recommendedTasks": [
    {
      "id": "tsk_101",
      "title": "Revisar informe mensual",
      "priority": "high"
    },
    {
      "id": "tsk_102",
      "title": "Responder correos importantes",
      "priority": "medium"
    }
  ]
}
```

---

## ⚡ 4️⃣ Módulo de Energía y Contexto

### **UC-11 Registrar Nivel de Energía**

**POST /energy**

**Request:**

```json
{
  "userId": "usr_001",
  "level": "low"
}
```

**Response:**

```json
{
  "userId": "usr_001",
  "level": "low",
  "registeredAt": "2025-11-12T14:00:00Z"
}
```

---

### **UC-12 Ajustar Prioridades según Energía**

**POST /ai/adjust-priority**

**Request:**

```json
{
  "userId": "usr_001",
  "energyLevel": "low"
}
```

**Response:**

```json
[
  {
    "taskId": "tsk_101",
    "newPriority": "low"
  },
  {
    "taskId": "tsk_103",
    "newPriority": "medium"
  }
]
```

---

## 📊 5️⃣ Módulo de Dashboard y Reportes

### **UC-13 Mostrar Dashboard**

**GET /dashboard**

**Response:**

```json
{
  "tasksCompleted": 18,
  "tasksPending": 7,
  "averageCompletionRate": 0.72,
  "energyTrend": ["high", "medium", "low", "medium"]
}
```

---

### **UC-03 Generar Reporte de Productividad**

**GET /reports/productivity?period=weekly**

**Response:**

```json
{
  "period": "weekly",
  "tasksCompleted": 12,
  "avgCompletionTimeHours": 5.3,
  "focusScore": 87,
  "recommendation": "Mantén tu ritmo actual y prioriza tareas cortas por la tarde."
}
```

---

## 🚀 Consideraciones técnicas

- Todos los endpoints (excepto `/auth/login` y `/users/register`) requieren **token JWT**.
    
- El sistema de IA puede estar desacoplado como microservicio (`/ai/*` endpoints).
    
- Las tareas y usuarios deben estar vinculados mediante `userId`.
