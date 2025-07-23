# 📊 Microsoft Fabric Simulator
## Simulador Interactivo de Bursting, Smoothing y Throttling

[![Deploy to Azure](https://img.shields.io/badge/Deploy%20to-Azure-blue?logo=microsoftazure)](https://green-glacier-0af1a471e.2.azurestaticapps.net)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/stuba83/fabric-simulator.svg)](https://github.com/stuba83/fabric-simulator/stargazers)

> **🚀 [DEMO EN VIVO](https://green-glacier-0af1a471e.2.azurestaticapps.net)** - ¡Prueba el simulador ahora!

---

## 🎯 **¿Qué es este simulador?**

Una herramienta interactiva que te permite **experimentar y comprender** los mecanismos críticos de Microsoft Fabric sin afectar tu entorno de producción:

- **🔥 Bursting:** Simula cómo Fabric permite usar temporalmente más CUs de los contratados
- **📈 Smoothing:** Visualiza cómo se distribuyen los overages durante 24 horas 
- **🚦 Throttling:** Experimenta cuándo y por qué se activan las restricciones de rendimiento

## ✨ **Características Principales**

### 🎮 **Escenarios Predefinidos**
- **🟢 Sin Smoothing** - Operaciones dentro del baseline
- **🔵 Burst + Smoothing** - Ejemplo básico de smoothing
- **🟡 Heavy Smoothing** - Burst intenso con distribución prolongada
- **🔴 Multiple Bursts** - Múltiples operaciones concurrentes
- **🟣 Weekend Smoothing** - Explica el "consumo fantasma" de fin de semana
- **🚫 Demo Throttling** - Muestra todos los tipos de throttling
- **🔓 Sin Throttling** - Comportamiento con capacidad adecuada
- **🏢 Enterprise Scale** - Escenarios de gran escala (F256)

### 🎛️ **Controles Configurables**
- **Capacidades SKU:** F2 hasta F256 (2-256 CUs)
- **Configuración de Burst:** Hora, intensidad y duración
- **Políticas de Throttling:** Interactive Delay/Rejection, Background Rejection
- **Actividad Base:** Simula carga de trabajo típica

### 📊 **Visualizaciones Avanzadas**
- **Gráfico Principal:** Comparación Real vs Smoothed utilization
- **Gráfico de Overages:** Acumulación y burn-down de overages
- **Análisis de Throttling:** Timeline detallado con causas específicas
- **Métricas en Tiempo Real:** Tooltips informativos por hora

## 🚀 **Acceso Rápido**

### **🌐 Versión Web (Recomendada)**
```
https://green-glacier-0af1a471e.2.azurestaticapps.net
```
*Funciona en cualquier navegador moderno - no requiere instalación*

### **💻 Ejecutar Localmente**
```bash
# Clonar el repositorio
git clone https://github.com/stuba83/fabric-simulator.git

# Navegar al directorio
cd fabric-simulator

# Abrir en navegador
open index.html
# O para Windows: start index.html
# O para Linux: xdg-open index.html
```

## 📖 **Cómo Usar el Simulador**

### **🔰 Para Principiantes**
1. **Visita** [el simulador en vivo](https://green-glacier-0af1a471e.2.azurestaticapps.net)
2. **Prueba** el escenario "🔵 Burst + Smoothing" 
3. **Observa** las diferencias entre utilización Real vs Smoothed
4. **Experimenta** con diferentes SKUs y configuraciones

### **🎯 Para Diagnóstico de Problemas**
1. **Replica tu entorno:** Ajusta SKU y carga base
2. **Simula tu workload:** Configura burst similar a tus operaciones
3. **Identifica causas:** Usa el timeline de throttling
4. **Optimiza:** Encuentra la configuración ideal

### **📏 Para Dimensionamiento**
1. **Empieza conservador:** F8 o F16 
2. **Incrementa carga:** Hasta ver throttling
3. **Encuentra el balance:** Entre costo y rendimiento
4. **Valida con scenarios reales:** Múltiples bursts, weekend smoothing

## 🎓 **Casos de Uso Reales**

### **🔍 Diagnóstico: "¿Por qué hay throttling?"**
```
Problema: Usuarios reportan queries lentos a las 2 PM
Solución: 
1. Usar "Demo Throttling" 
2. Configurar burst a las 14:00
3. Identificar Interactive Delay como causa
4. Ajustar horarios o escalar capacidad
```

### **👻 Diagnóstico: "Consumo fantasma en weekends"**
```
Problema: Fabric consume CUs los sábados sin actividad
Solución:
1. Usar "Weekend Smoothing"
2. Configurar burst viernes a las 18:00  
3. Observar smoothing hasta el domingo
4. Explicar a stakeholders con gráficos
```

### **💰 Optimización: "¿Necesitamos F32 o basta F16?"**
```
Objetivo: Validar dimensionamiento antes de upgrade
Proceso:
1. Simular carga actual en F16
2. Incrementar burst hasta ver throttling crítico
3. Comparar con F32 sin throttling
4. Calcular ROI del upgrade
```

## 🛠️ **Tecnologías Utilizadas**

- **Frontend:** HTML5, CSS3, JavaScript (Vanilla)
- **Gráficos:** Chart.js 3.9.1
- **Hosting:** Azure Static Web Apps
- **CI/CD:** GitHub Actions
- **Responsive:** Compatible con móviles y tablets

## 📊 **Documentación Técnica**

### **Conceptos Implementados**
- **Bursting Factor:** Cálculo según documentación oficial de Microsoft
- **Smoothing de 24h:** Para operaciones background según políticas reales
- **Throttling Progresivo:** Interactive Delay → Interactive Rejection → Background Rejection
- **Future Capacity:** Métrica crítica para background job throttling

### **Configuraciones por Defecto**
```javascript
Interactive Delay: 10 minutos
Interactive Rejection: 60 minutos  
Background Rejection: 24 horas
Overage Protection: 10 minutos
```

## 🤝 **Contribuir**

### **🐛 Reportar Issues**
- Usa [GitHub Issues](https://github.com/stuba83/fabric-simulator/issues)
- Incluye screenshot y pasos para reproducir
- Especifica navegador y versión

### **✨ Sugerir Mejoras**
- Fork el repositorio
- Crear feature branch: `git checkout -b feature/nueva-funcionalidad`
- Commit cambios: `git commit -m 'Add nueva funcionalidad'`
- Push branch: `git push origin feature/nueva-funcionalidad`
- Abrir Pull Request

### **📝 Mejoras Deseadas**
- [ ] Exportar configuraciones como JSON
- [ ] Importar datos de Fabric Capacity Metrics App
- [ ] Simulador de múltiples workspaces
- [ ] Calculadora de costos integrada
- [ ] Comparador de SKUs lado a lado

## 📚 **Recursos Relacionados**

### **📖 Documentación Official Microsoft**
- [Fabric Throttling](https://learn.microsoft.com/en-us/fabric/enterprise/throttling)
- [Capacity Metrics App](https://learn.microsoft.com/en-us/fabric/enterprise/metrics-app)
- [Burstable Capacity](https://learn.microsoft.com/en-us/fabric/data-warehouse/burstable-capacity)

### **🎥 Videos y Tutoriales**
- [Fabric Capacity Planning](https://learn.microsoft.com/en-us/fabric/enterprise/plan-capacity)
- [Optimize Capacity](https://learn.microsoft.com/en-us/fabric/enterprise/optimize-capacity)

### **🛠️ Herramientas Complementarias**
- [Fabric SKU Estimator](https://learn.microsoft.com/en-us/fabric/enterprise/fabric-sku-estimator)
- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

## 📜 **Licencia**

Este proyecto está licenciado bajo la Licencia MIT - ver el archivo [LICENSE](LICENSE) para detalles.

## 👨‍💻 **Autor**

**stuba83** - [GitHub Profile](https://github.com/stuba83)

---

## 🌟 **¿Te resultó útil?**

Si este simulador te ayudó a entender mejor Microsoft Fabric:
- ⭐ **Dale una estrella** a este repositorio
- 🔄 **Compártelo** con tu equipo
- 💬 **Deja feedback** en Issues
- 🤝 **Contribuye** con mejoras

---

### 📊 **Stats del Proyecto**

![GitHub last commit](https://img.shields.io/github/last-commit/stuba83/fabric-simulator)
![GitHub repo size](https://img.shields.io/github/repo-size/stuba83/fabric-simulator)
![Website](https://img.shields.io/website?url=https://green-glacier-0af1a471e.2.azurestaticapps.net)

**🎯 Hecho con ❤️ para la comunidad de Microsoft Fabric**