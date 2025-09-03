# WordPress Custom Helm Chart

Este es un Helm Chart personalizado para WordPress con SSL automático usando cert-manager, optimizado para su uso en Rancher.

## Características

- ✅ SSL automático con cert-manager y Let's Encrypt
- ✅ Configuración simplificada a través de la UI de Rancher
- ✅ Base de datos MySQL incluida (interna o externa)
- ✅ Almacenamiento persistente para WordPress y base de datos
- ✅ Recursos configurables con límites apropiados
- ✅ Health checks para WordPress y MySQL
- ✅ Compatible con la interfaz de Rancher

## Requisitos Previos

1. **cert-manager** instalado en el cluster
2. **ClusterIssuer** configurado (ej: `letsencrypt-prod`)
3. **Ingress Controller** (nginx recomendado)
4. **Storage Class** configurado para PVCs

## Instalación desde Rancher UI

### Paso 1: Añadir Repositorio del Chart

1. En Rancher, ve a **Apps & Marketplace** > **Chart Repositories**
2. Haz clic en **Create**
3. Configura:
   - **Name**: `wordpress-custom-repo`
   - **Target**: Git Repository
   - **Git Repo URL**: `[URL_DE_TU_REPOSITORIO]`
   - **Git Branch**: `main`

### Paso 2: Instalar el Chart

1. Ve a **Apps & Marketplace** > **Charts**
2. Busca `wordpress-custom`
3. Haz clic en **Install**
4. Completa los campos requeridos:

#### Configuración Básica
- **Dominio**: Tu dominio (ej: `miweb.com`)
- **Título del Sitio**: Nombre de tu sitio WordPress
- **Usuario Admin**: Usuario administrador
- **Contraseña Admin**: Contraseña segura (mín. 8 caracteres)
- **Email Admin**: Email del administrador

#### SSL (Opcional)
- **SSL Automático**: Habilitado por defecto
- **Issuer SSL**: `letsencrypt-prod` (o tu ClusterIssuer)

#### Base de Datos (Opcional)
- **Tipo de BD**: `internal` (MySQL incluido) o `external`
- **Nombre BD**: `wordpress` (por defecto)
- **Usuario BD**: `wordpress` (por defecto)
- **Contraseña BD**: Contraseña para la base de datos

#### Almacenamiento (Opcional)
- **Tamaño Storage**: `10Gi` (por defecto)
- **Storage Class**: Dejar vacío para usar el default

#### Recursos (Opcional)
- **Memoria Mínima**: `512Mi`
- **CPU Mínima**: `250m`
- **Límite Memoria**: `1Gi`
- **Límite CPU**: `500m`

## Instalación Vía Helm CLI

```bash
# Añadir repositorio
helm repo add wordpress-custom [URL_REPOSITORIO]
helm repo update

# Instalar con valores básicos
helm install mi-wordpress wordpress-custom/wordpress-custom \
  --set app.domain=miweb.com \
  --set app.adminPassword=MiPassword123 \
  --set database.password=DbPassword123
```

## Configuración Post-Instalación

1. **Verificar certificado SSL**: Puede tardar unos minutos en generarse
2. **Acceder a WordPress**: `https://[tu-dominio]/wp-admin`
3. **Completar instalación**: Seguir el asistente de WordPress

## Personalización

### Cambiar versión de WordPress
```yaml
image:
  tag: "6.4-apache"  # Cambiar versión aquí
```

### Usar base de datos externa
```yaml
database:
  type: "external"
  host: "mysql.ejemplo.com"
  name: "mi_wordpress"
  user: "wp_user"
  password: "password_seguro"
```

### Configurar recursos personalizados
```yaml
resources:
  requests:
    memory: "1Gi"
    cpu: "500m"
  limits:
    memory: "2Gi"
    cpu: "1000m"
```

## Troubleshooting

### SSL no funciona
1. Verificar que cert-manager esté instalado
2. Comprobar el ClusterIssuer: `kubectl get clusterissuer`
3. Revisar el certificado: `kubectl get certificate`

### WordPress no inicia
1. Verificar logs: `kubectl logs deployment/[release-name]`
2. Comprobar PVC: `kubectl get pvc`
3. Verificar conectividad con base de datos

### Base de datos no accesible
1. Si es interna, verificar logs MySQL: `kubectl logs deployment/[release-name]-mysql`
2. Si es externa, verificar conectividad de red
3. Comprobar credenciales en el Secret

## Valores por Defecto

```yaml
# Imagen de WordPress
image:
  repository: wordpress
  tag: "6.4-apache"

# SSL habilitado por defecto
ssl:
  enabled: true
  issuer: "letsencrypt-prod"

# Almacenamiento persistente
persistence:
  size: "10Gi"

# Base de datos MySQL interna
database:
  type: "internal"
mysql:
  persistence:
    size: "8Gi"

# Recursos moderados
resources:
  requests:
    memory: "512Mi"
    cpu: "250m"
  limits:
    memory: "1Gi"
    cpu: "500m"
```

## Soporte

Para problemas o mejoras, crear un issue en el repositorio del chart.
