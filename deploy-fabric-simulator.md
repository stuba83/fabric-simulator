# 🚀 Despliegue del Simulador de Microsoft Fabric en Azure

## Opción 1: Azure Static Web Apps (Recomendado)

### 📋 **Prerrequisitos**
- Cuenta de Azure activa
- Cuenta de GitHub (para CI/CD automático)
- Visual Studio Code o cualquier editor

---

## 🛠️ **Método A: Despliegue Directo desde Portal Azure**

### **Paso 1: Preparar archivos localmente**

1. **Crear estructura de carpetas:**
```
fabric-simulator/
├── index.html          (tu simulador)
├── README.md           (opcional)
└── .gitignore          (opcional)
```

2. **Guardar el simulador como `index.html`:**
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Microsoft Fabric: Simulador de Bursting, Smoothing y Throttling</title>
    <!-- Copiar todo el contenido del artefacto aquí -->
</head>
<body>
    <!-- Resto del contenido del simulador -->
</body>
</html>
```

### **Paso 2: Crear repositorio en GitHub**

1. **En GitHub.com:**
   - Click "New repository"
   - Nombre: `fabric-simulator`
   - Público o privado (tu elección)
   - Initialize with README ✅

2. **Subir archivos:**
   ```bash
   git clone https://github.com/TU_USUARIO/fabric-simulator.git
   cd fabric-simulator
   # Copiar tu index.html aquí
   git add .
   git commit -m "Initial commit: Fabric simulator"
   git push origin main
   ```

### **Paso 3: Crear Azure Static Web App**

1. **En Azure Portal (portal.azure.com):**
   - Search: "Static Web Apps"
   - Click "Create"

2. **Configuración básica:**
   ```
   Subscription: [Tu suscripción]
   Resource Group: [Crear nuevo: "rg-fabric-simulator"]
   Name: "fabric-simulator-app"
   Plan Type: Free (para desarrollo)
   Region: East US 2 (o la más cercana)
   ```

3. **Configuración de deployment:**
   ```
   Source: GitHub
   GitHub Account: [Autorizar tu cuenta]
   Organization: [Tu usuario]
   Repository: fabric-simulator
   Branch: main
   Build Presets: Custom
   App location: / 
   Api location: (dejar vacío)
   Output location: (dejar vacío)
   ```

4. **Click "Review + Create" → "Create"**

### **Paso 4: Verificar despliegue**

1. **Azure creará automáticamente:**
   - GitHub Action para CI/CD
   - Certificado SSL gratuito
   - URL pública

2. **En 2-3 minutos tendrás:**
   - URL: `https://[nombre-generado].azurestaticapps.net`
   - SSL automático
   - Actualizaciones automáticas con cada push

---

## 🛠️ **Método B: Azure CLI (Para usuarios avanzados)**

### **Paso 1: Instalar Azure CLI**
```bash
# Windows (PowerShell)
winget install Microsoft.AzureCLI

# macOS
brew install azure-cli

# Linux Ubuntu/Debian
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### **Paso 2: Login y configuración**
```bash
# Login a Azure
az login

# Verificar suscripción
az account show

# Crear resource group
az group create \
  --name rg-fabric-simulator \
  --location eastus2
```

### **Paso 3: Crear Static Web App**
```bash
# Instalar extensión
az extension add --name staticwebapp

# Crear app
az staticwebapp create \
  --name fabric-simulator-app \
  --resource-group rg-fabric-simulator \
  --source https://github.com/TU_USUARIO/fabric-simulator \
  --location eastus2 \
  --branch main \
  --app-location "/" \
  --login-with-github
```

---

## 🛠️ **Método C: Despliegue Manual (Sin GitHub)**

### **Paso 1: Usar Azure Storage Static Website**

1. **Crear Storage Account:**
   ```bash
   az storage account create \
     --name fabricsimulatorstorage \
     --resource-group rg-fabric-simulator \
     --location eastus2 \
     --sku Standard_LRS
   ```

2. **Habilitar static website:**
   ```bash
   az storage blob service-properties update \
     --account-name fabricsimulatorstorage \
     --static-website \
     --index-document index.html
   ```

3. **Subir archivos:**
   ```bash
   az storage blob upload \
     --account-name fabricsimulatorstorage \
     --container-name '$web' \
     --name index.html \
     --file index.html
   ```

4. **Obtener URL:**
   ```bash
   az storage account show \
     --name fabricsimulatorstorage \
     --query "primaryEndpoints.web" \
     --output tsv
   ```

---

## 🔧 **Configuraciones Adicionales**

### **Custom Domain (Opcional)**

1. **En Azure Static Web Apps:**
   - Settings → Custom domains
   - Add custom domain
   - Verify ownership (DNS record)

2. **Ejemplo con dominio propio:**
   ```
   simulator.tuempresa.com → Azure Static Web App
   ```

### **Configuración de Azure Front Door (Opcional)**

Para mejor rendimiento global:

```bash
# Crear Front Door profile
az afd profile create \
  --profile-name fabric-simulator-fd \
  --resource-group rg-fabric-simulator \
  --sku Standard_AzureFrontDoor
```

### **Variables de entorno (Si necesitas APIs)**

En `staticwebapp.config.json`:
```json
{
  "environmentVariables": {
    "API_URL": "https://api.tudominio.com"
  },
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

---

## 💰 **Estimación de Costos**

### **Azure Static Web Apps (Free Tier):**
- ✅ 100 GB bandwidth/mes
- ✅ 0.5 GB storage
- ✅ SSL certificate incluido
- ✅ Custom domains ilimitados
- **Costo: $0/mes**

### **Azure Static Web Apps (Standard):**
- ✅ 100 GB bandwidth incluido
- ✅ 0.5 GB storage incluido
- ✅ Extra bandwidth: $0.15/GB
- **Costo: ~$9/mes + bandwidth**

### **Azure Storage Static Website:**
- ✅ Storage: $0.018/GB/mes
- ✅ Bandwidth: $0.087/GB
- ✅ Operations: $0.004/10,000
- **Costo: ~$1-5/mes** (dependiendo del tráfico)

---

## 🔒 **Seguridad y Mejores Prácticas**

### **Configurar HTTPS redirect**
En `staticwebapp.config.json`:
```json
{
  "globalHeaders": {
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains"
  },
  "routes": [
    {
      "route": "/*",
      "headers": {
        "X-Frame-Options": "DENY",
        "X-Content-Type-Options": "nosniff"
      }
    }
  ]
}
```

### **Configurar cache headers**
```json
{
  "routes": [
    {
      "route": "*.js",
      "headers": {
        "Cache-Control": "public, max-age=604800"
      }
    },
    {
      "route": "*.css", 
      "headers": {
        "Cache-Control": "public, max-age=604800"
      }
    }
  ]
}
```

---

## 🚀 **Pasos de Implementación Recomendados**

### **Para principiantes:**
1. ✅ Método A (Portal Azure + GitHub)
2. ✅ Free tier de Static Web Apps
3. ✅ Usar dominio autogenerado inicialmente

### **Para equipos:**
1. ✅ Método A con repositorio de organización
2. ✅ Configurar branch protection rules
3. ✅ Añadir custom domain
4. ✅ Configurar monitoring con Application Insights

### **Para producción:**
1. ✅ Standard tier de Static Web Apps
2. ✅ Custom domain con certificado SSL
3. ✅ Azure Front Door para CDN global
4. ✅ Monitoring y alertas configuradas

---

## 📱 **Verificación Post-Despliegue**

### **Checklist de pruebas:**
- [ ] Página carga correctamente
- [ ] Todos los controles funcionan
- [ ] Gráficos se renderizan
- [ ] Tooltips muestran información
- [ ] Responsive design en móvil
- [ ] SSL certificate válido
- [ ] Performance aceptable (<3s load time)

### **URLs importantes a guardar:**
- **Sitio web:** `https://[tu-app].azurestaticapps.net`
- **Azure Portal:** Portal de gestión del recurso
- **GitHub Actions:** Para ver deployments
- **Domain management:** Si usas custom domain

---

## 🆘 **Troubleshooting Común**

### **Error: "Site not found"**
- Verificar que index.html está en root directory
- Check GitHub Action logs
- Confirmar branch configurada correctamente

### **Error: "Charts not loading"**
- Verificar que Chart.js CDN está accesible
- Check browser console for JavaScript errors
- Confirmar CORS settings si usas APIs externas

### **Error: "Build failed"**
- Revisar GitHub Action logs
- Verificar sintaxis HTML válida
- Check que no hay archivos con caracteres especiales

---

**🎯 Recomendación:** Empieza con el **Método A** usando GitHub y Azure Static Web Apps Free tier. Es la opción más simple, con CI/CD automático y cero configuración de infraestructura.