
## 🎯 Explicación Detallada de Campos Clave

### **Tabla `users`:
- `preferences (JSONB)`: Flexible para nuevas preferencias sin modificar esquema
- `energy_level`: Para personalización inicial antes de tener logs detallados
- `last_login`: Útil para analytics de engagement
### **Tabla `tasks`:**
- `urgency/impact`: Inputs directos para el algoritmo de IA
- `priority_score (1-100)`: Puntuación numérica para ordenamiento preciso
- `priority_level`: Clasificación categórica para visualización
- `completion_probability`: Salida del modelo de PyTorch
- `status`: Soporta el workflow completo (archivado, pospuesto)
- `actual_duration`: Para comparar con estimaciones y mejorar IA
### **Tabla `task_history`:**
- `change_type`: Categoriza el tipo de cambio para analytics
- `old_values/new_values (JSONB)`: Captura estado completo antes/después
- Escalable: No requiere cambios al añadir nuevos campos a tasks
### **Tabla `daily_recommendations`:
- `confidence_score`: Certeza de la IA en su recomendación
- `was_completed/completed_on_time`: Para medir efectividad de recomendaciones
- `UNIQUE(user_id, recommendation_date)`: Garantiza una recomendación diaria
### **Tabla `ai_models`:**
- `model_data (BYTEA)`: Almacena modelos serializados de Scikit-Learn/PyTorch
- `feature_weights (JSONB)`: Explicabilidad - qué factores pesan más
- `user_id NULL`: Soporta modelos globales y personalizados


### Tabla de usuarios

```
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(100) NOT NULL,
    -- Preferencias del usuario (almacenadas como JSON para flexibilidad)
    preferences JSONB DEFAULT '{
        "notifications": true,
        "energy_tracking": false,
        "default_view": "priority"
    }',
    energy_level VARCHAR(20) DEFAULT 'medium' CHECK (energy_level IN ('low', 'medium', 'high')),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP,
    is_active BOOLEAN DEFAULT true
);
```


### Tabla de categorías (creadas por usuarios)

```
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    color VARCHAR(7) DEFAULT '#007bff', -- Color hexadecimal
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(user_id, name) -- Evita categorías duplicadas por usuario
);

```

### Tabla principal de tareas

```
CREATE TABLE tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    category_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    
    -- Información básica de la tarea
    title VARCHAR(200) NOT NULL,
    description TEXT,
    
    -- Atributos para la IA
    urgency VARCHAR(20) CHECK (urgency IN ('low', 'medium', 'high')),
    impact VARCHAR(20) CHECK (impact IN ('low', 'medium', 'high')),
    estimated_duration INTEGER, -- en minutos
    deadline TIMESTAMP,
    
    -- Resultados de la IA
    priority_score INTEGER CHECK (priority_score >= 1 AND priority_score <= 100),
    priority_level VARCHAR(20) CHECK (priority_level IN ('low', 'medium', 'high')),
    completion_probability DECIMAL(5,4) CHECK (completion_probability >= 0 AND completion_probability <= 1),
    
    -- Metadatos de la tarea
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'in_progress', 'completed', 'archived', 'postponed')),
    energy_required VARCHAR(20) CHECK (energy_required IN ('low', 'medium', 'high')),
    
    -- Timestamps
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP,
    actual_duration INTEGER, -- en minutos (se llena al completar)
    
  
);
```

```
-- Índices para búsquedas rápidas
    CREATE INDEX idx_user_priority (user_id, priority_level),
    CREATE INDEX idx_user_status (user_id, status),
    CREATE INDEX idx_user_deadline (user_id, deadline)
```

### Tabla de historial de cambios (sistema de trazas) -- Descartada

```
CREATE TABLE task_history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    task_id UUID NOT NULL REFERENCES tasks(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    -- Qué cambió (almacenamos el cambio específico)
    change_type VARCHAR(50) NOT NULL, -- 'created', 'updated', 'status_changed', 'priority_recalculated'
    old_values JSONB, -- Estado anterior (opcional, para cambios complejos)
    new_values JSONB, -- Estado nuevo (opcional)
    change_description TEXT,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
);
```

```
CREATE INDEX idx_task_history (task_id, created_at)
```

###  Tabla de recomendaciones diarias

```
CREATE TABLE daily_recommendations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    task_id UUID NOT NULL REFERENCES tasks(id) ON DELETE CASCADE,
    
    -- Lógica de la recomendación
    recommendation_reason TEXT NOT NULL,
    confidence_score DECIMAL(5,4) CHECK (confidence_score >= 0 AND confidence_score <= 1),
    
    -- Estado de la recomendación
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'accepted', 'rejected', 'postponed')),
    
    -- Seguimiento de efectividad
    was_completed BOOLEAN,
    completed_on_time BOOLEAN,
    
    recommendation_date DATE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    UNIQUE(user_id, recommendation_date) -- una recomendación por día
);

```

```
CREATE INDEX idx_user_recommendations
ON daily_recommendations (user_id, recommendation_date);
CREATE INDEX idx_recommendation_date ON daily_recommendations (recommendation_date);

```
### Tabla de registros de energía del usuario

```
CREATE TABLE energy_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    task_id UUID REFERENCES tasks(id) ON DELETE SET NULL,
    
    energy_level VARCHAR(20) NOT NULL CHECK (energy_level IN ('low', 'medium', 'high')),
    notes TEXT,
    
    logged_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

```

```
	CREATE INDEX idx_energy_user ON energy_logs (user_id);
	CREATE INDEX idx_energy_date ON energy_logs (logged_at);

    ---CREATE INDEX idx_user_energy (user_id, logged_at)
```
###  Tabla de modelos de IA (para el aprendizaje continuo)
``
```
CREATE TABLE ai_models (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE, -- NULL para modelo global
    model_type VARCHAR(50) NOT NULL, -- 'priority_classifier', 'completion_predictor'
    model_version VARCHAR(20) NOT NULL,
    
    -- Datos del modelo
    model_data BYTEA, -- Modelo serializado
    feature_weights JSONB, -- Pesos/importancia de características
    accuracy_metrics JSONB,
    
    -- Metadata
    is_active BOOLEAN DEFAULT false,
    trained_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_user_models (user_id, model_type)
);
```

###  Tabla de ml_feedback para mejora de IA

```
CREATE TABLE ml_feedback
(
    id uuid NOT NULL,
    task_id uuid NOT NULL,
    user_id uuid NOT NULL,
    feedback_type character varying(50) COLLATE pg_catalog."default",
    was_useful boolean,
    actual_priority character varying(20) COLLATE pg_catalog."default",
    actual_completion_time integer,
    created_at timestamp without time zone,
    CONSTRAINT ml_feedback_pkey PRIMARY KEY (id),
    CONSTRAINT ml_feedback_task_id_fkey FOREIGN KEY (task_id)
        REFERENCES public.tasks (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE CASCADE,
    CONSTRAINT ml_feedback_user_id_fkey FOREIGN KEY (user_id)
        REFERENCES public.users (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE CASCADE
)

```

### Tabla task_ml_data para registrar datos para la IA:

```
CREATE TABLE task_ml_data
(
    id uuid NOT NULL,
    task_id uuid NOT NULL,
    user_id uuid NOT NULL,
    ml_priority_score numeric(5,4),
    predicted_completion_time integer,
    recommended_schedule character varying(50) COLLATE pg_catalog."default",
    features jsonb,
    created_at timestamp without time zone,
    updated_at timestamp without time zone,
    CONSTRAINT task_ml_data_pkey PRIMARY KEY (id),
    CONSTRAINT task_ml_data_task_id_fkey FOREIGN KEY (task_id)
        REFERENCES public.tasks (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE CASCADE,
    CONSTRAINT task_ml_data_user_id_fkey FOREIGN KEY (user_id)
        REFERENCES public.users (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE CASCADE
)
```


### Tabla ai_feedback antigua tabla para obtener feedbback en el anterior módelo

```
CREATE TABLE ai_feedback
(
    id uuid NOT NULL,
    user_id uuid NOT NULL,
    task_id uuid NOT NULL,
    predicted_priority character varying(20) COLLATE pg_catalog."default",
    actual_priority character varying(20) COLLATE pg_catalog."default",
    predicted_completion_probability numeric(5,4),
    actual_completed boolean,
    completed_on_time boolean,
    feedback_date timestamp without time zone,
    used_for_training boolean,
    CONSTRAINT ai_feedback_pkey PRIMARY KEY (id),
    CONSTRAINT ai_feedback_task_id_fkey FOREIGN KEY (task_id)
        REFERENCES public.tasks (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE CASCADE,
    CONSTRAINT ai_feedback_user_id_fkey FOREIGN KEY (user_id)
        REFERENCES public.users (id) MATCH SIMPLE
        ON UPDATE NO ACTION
        ON DELETE CASCADE
)

```


## Esquema ERD: 

![[ERD Smart task less information.png]]