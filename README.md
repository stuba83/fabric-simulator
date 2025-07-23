# 📊 Guía Completa del Simulador de Microsoft Fabric
## Bursting, Smoothing y Throttling Explicados

---

## 🎯 **Propósito del Simulador**

Este simulador te permite **experimentar y comprender** los mecanismos críticos de Microsoft Fabric:
- **Bursting:** Cómo Fabric permite usar temporalmente más CUs de los contratados
- **Smoothing:** Cómo se distribuyen los overages durante 24 horas para evitar picos de facturación
- **Throttling:** Cuándo y por qué se activan las restricciones de rendimiento

---

## 🏗️ **Arquitectura del Simulador**

### **📊 Gráfico Principal**
Muestra la comparación hora por hora de:
- **🟢 Línea Verde (Baseline Capacity):** CUs contratados según tu SKU
- **🔴 Línea Roja (Utilización Real):** Consumo real sin smoothing - muestra los picos reales
- **🟡 Línea Amarilla (Utilización Smoothed):** Lo que realmente se factura después del smoothing
- **🟣 Zona Púrpura (Throttling Zone):** Áreas donde se activa throttling

### **⚡ Gráfico de Overages**
Visualiza el ciclo completo de overages:
- **📊 Barras Azules (Overage Generado):** Momento exacto cuando se crea el overage
- **🟠 Línea Naranja (Overage Acumulado):** Cuánto overage queda por "quemar"
- **🌸 Línea Rosa (Future Capacity):** Capacidad futura consumida (crítica para throttling)

---

## 🎛️ **Controles Principales**

### **⚙️ Configuración Básica**

| Control | Función | Valores Recomendados |
|---------|---------|---------------------|
| **Capacidad SKU** | Define CUs baseline disponibles | F8 (8 CUs) para pruebas generales |
| **Hora del Burst** | Cuándo ocurre la operación intensa | 9 AM (horario laboral típico) |
| **CUs del Burst** | Intensidad de la operación | 2-4x el baseline para ver efectos claros |
| **Duración** | Tiempo de la operación intensa | 30-60 minutos para workloads típicos |
| **Actividad Base** | Carga constante de background | 40% para simular uso normal |
| **Ops Adicionales** | Otras operaciones concurrentes | "Moderadas" para escenarios realistas |

### **🚦 Controles de Throttling**

| Parámetro | Significado | Valor Por Defecto | Propósito |
|-----------|-------------|-------------------|-----------|
| **Interactive Delay** | Tiempo hasta que queries interactivos se ralentizan | 10 min | Política oficial de Microsoft |
| **Interactive Rejection** | Tiempo hasta rechazo total de queries | 60 min | Límite crítico documentado |
| **Background Rejection** | Límite de future capacity para jobs background | 24 horas | Previene acumulación excesiva |
| **Overage Protection** | Tiempo libre de throttling al exceder baseline | 10 min | "Colchón" oficial de Microsoft |

---

## 🎮 **Escenarios Predefinidos**

### **🟢 Sin Smoothing**
- **Propósito:** Mostrar operaciones que NO generan overages
- **Configuración:** Burst pequeño dentro del baseline
- **Aprendizaje:** Cuándo NO necesitas preocuparte por smoothing

### **🔵 Burst + Smoothing**
- **Propósito:** Ejemplo básico de smoothing en acción
- **Configuración:** Burst moderado que genera overage manejable
- **Aprendizaje:** Cómo 1 hora de burst se distribuye en 24 horas

### **🟡 Heavy Smoothing**
- **Propósito:** Burst grande con smoothing significativo
- **Configuración:** Burst 4-8x el baseline
- **Aprendizaje:** Impacto de operaciones muy intensas en días posteriores

### **🔴 Multiple Bursts**
- **Propósito:** Múltiples operaciones intensas en el mismo día
- **Configuración:** Capacity más grande con múltiples picos
- **Aprendizaje:** Cómo se acumulan overages de diferentes operaciones

### **🟣 Weekend Smoothing**
- **Propósito:** Demostrar por qué hay consumo en fines de semana
- **Configuración:** Burst de viernes que afecta el weekend
- **Aprendizaje:** Resolver el misterio del "consumo fantasma"

### **🚫 Demo Throttling**
- **Propósito:** Activar todos los tipos de throttling
- **Configuración:** Capacity pequeña con burst masivo
- **Aprendizaje:** Consecuencias de subdimensionar capacidad

### **🔓 Sin Throttling**
- **Propósito:** Mostrar comportamiento ideal sin restricciones
- **Configuración:** Capacity grande que nunca se satura
- **Aprendizaje:** Beneficios de capacidades bien dimensionadas

### **🏢 Enterprise Scale**
- **Propósito:** Comportamiento a escala empresarial
- **Configuración:** F256 con workloads masivos
- **Aprendizaje:** Dinámicas de organizaciones grandes

---

## 🔍 **Interpretando los Resultados**

### **📈 Panel de Análisis de Smoothing**

**✅ Smoothing Activo:**
```
Overage Total: 12.5 CU-hours
Distribución: 0.52 CUs/hora por 24h
Peak Acumulado: 12.5 CU-hours
Remanente Final: 0.0 CU-hours
```

**Interpretación:**
- Una operación generó 12.5 CU-hours de overage
- Se distribuye como +0.52 CUs adicionales cada hora
- Al final de 24h, todo el overage se "quemó"

### **🚦 Panel de Análisis de Throttling**

**🚨 Estados de Throttling:**
- **📊 Interactive Delay:** Queries lentos pero ejecutándose
- **🚫 Interactive Rejection:** Queries rechazados completamente  
- **⛔ Background Rejection:** Jobs background bloqueados
- **🛡️ Protección:** Período de gracia sin throttling

### **📊 Timeline de Throttling**
Tabla que muestra:
- **Hora:** Cuándo ocurrió cada evento
- **Util%:** Porcentaje de utilización vs baseline
- **Severidad:** Nivel de impacto (Warning/Danger/Critical)
- **Estado:** Tipo específico de throttling activo

---

## 💡 **Casos de Uso Prácticos**

### **🔬 Diagnóstico de Problemas**

**Problema:** "Tenemos throttling y no sabemos por qué"
1. Usa el escenario **Demo Throttling**
2. Observa el timeline para identificar patrones
3. Ajusta la configuración hasta replicar tu situación real
4. Identifica las causas específicas en el panel de análisis

**Problema:** "Hay consumo en weekends sin actividad"
1. Usa el escenario **Weekend Smoothing** 
2. Ve cómo un burst de viernes afecta sábado y domingo
3. Explica el fenómeno a stakeholders usando los gráficos

### **📏 Dimensionamiento de Capacidad**

**Objetivo:** Determinar el SKU apropiado
1. Empieza con tu carga real estimada
2. Usa **Sin Throttling** como objetivo ideal
3. Encuentra el SKU mínimo que evita throttling crítico
4. Considera el costo vs benefit de capacidades mayores

### **⏰ Planificación de Workloads**

**Objetivo:** Optimizar horarios de ejecución
1. Simula diferentes horas de burst
2. Observa cómo afecta el smoothing posterior
3. Encuentra ventanas óptimas que minimizan conflictos
4. Planifica operaciones críticas en horarios de baja actividad

---

## 🎯 **Tips para Uso Efectivo**

### **🔄 Flujo de Trabajo Recomendado**

1. **Exploración Inicial**
   - Prueba todos los escenarios predefinidos
   - Familiarízate con los conceptos básicos
   - Observa las diferencias entre Real vs Smoothed

2. **Replicación de tu Entorno**
   - Ajusta SKU a tu capacidad actual
   - Configura burst similar a tus workloads reales
   - Establece actividad base según tu uso típico

3. **Experimentación Dirigida**
   - Cambia un parámetro a la vez
   - Observa el impacto en throttling
   - Documenta configuraciones que funcionan bien

4. **Análisis de Escenarios**
   - Usa tooltips para detalles por hora
   - Compara métricas entre configuraciones
   - Identifica puntos de inflexión críticos

### **⚠️ Interpretaciones Importantes**

**Smoothing NO afecta rendimiento:**
- La operación original corre a máxima velocidad
- Solo cambia CUÁNDO se factura el consumo
- El smoothing es transparente para el usuario final

**Throttling SÍ afecta rendimiento:**
- Interactive Delay = queries más lentos
- Interactive Rejection = queries completamente bloqueados
- Background Rejection = jobs diferidos o cancelados

**Future Capacity es clave:**
- Es la métrica crítica para throttling de background jobs
- Se acumula con cada overage
- Solo se reduce cuando hay capacidad idle

---

## 🚨 **Señales de Alerta**

### **🔴 Throttling Crítico**
- Background Rejection activo por >4 horas
- Interactive Rejection durante horarios laborales
- Future Capacity >48 horas acumuladas

### **🟡 Throttling Preventivo**
- Interactive Delay frecuente
- Utilización >120% durante >2 horas
- Overage Protection agotándose regularmente

### **🟢 Configuración Saludable**
- Throttling ocasional y breve
- Smoothing distribuyendo overages efectivamente
- Future Capacity manteniéndose <24 horas

---

## 📚 **Conceptos Clave para Recordar**

### **🎯 Bursting**
- ✅ Permite superar baseline temporalmente
- ✅ Acelera operaciones críticas
- ⚠️ Genera overage que debe "pagarse" después

### **📊 Smoothing**
- ✅ Distribuye overages durante 24 horas
- ✅ Reduce picos de facturación
- ⚠️ Puede causar throttling horas después del burst original

### **🚦 Throttling**
- ✅ Protege la plataforma de sobrecarga
- ✅ Aplica políticas progresivas y justas
- ⚠️ Puede impactar significativamente el rendimiento

### **💰 Implicaciones de Costos**
- Baseline = costo fijo mensual
- Overage = NO genera costo adicional (se distribuye)
- Throttling = costo de oportunidad y productividad

---

## 🔗 **Recursos Adicionales**

Para profundizar en estos conceptos, consulta:
- [Documentación oficial de throttling](https://learn.microsoft.com/en-us/fabric/enterprise/throttling)
- [Fabric Capacity Metrics App](https://learn.microsoft.com/en-us/fabric/enterprise/metrics-app)
- [Burstable Capacity en Data Warehouse](https://learn.microsoft.com/en-us/fabric/data-warehouse/burstable-capacity)

---

*Esta guía acompaña al simulador interactivo y debe usarse como referencia durante la experimentación práctica con diferentes escenarios y configuraciones.*