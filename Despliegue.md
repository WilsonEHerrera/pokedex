# Proceso de Despliegue - PokeDex en Azure Static Web Apps

Documentación técnica detallada del proceso de despliegue de la aplicación PokeDex en Azure Static Web Apps, incluyendo comandos, configuraciones, errores encontrados y soluciones aplicadas.

---

## Pre-requisitos

| Herramienta | Versión | Propósito |
|-------------|---------|-----------|
| Node.js | v24.11.1 | Entorno de ejecución para Angular |
| Angular CLI | - | Compilación del proyecto |
| Git |  2.49.0.windows.1 | Control de versiones |
| Cuenta GitHub | - | Repositorio del código |
| Cuenta Azure for Students | - | Plataforma de despliegue |

---

## Paso 1: Obtener el código fuente

Se clonó el repositorio del suministrado en la guia para obtener el código fuente de la aplicación:

```bash
git clone https://github.com/rcuello/ac4dem1a.git
```

El código fuente de la PokeDex se encuentra en la ruta:

```
ac4dem1a/sistemas-distribuidos/poke-dex-lab/source/pokedex-angular/
```

---

## Paso 2: Instalar dependencias

```bash
npm install
```

## Paso 3: Compilar el proyecto

Se generó la carpeta de despliegue con el comando:

```bash
npm run build
```

Al finalizar se creó la carpeta `dist/pokedex-angular/` con todos los archivos listos para desplegar:

```
dist/
  pokedex-angular/
      index.html
      assets/
      ...
```

---

## Paso 4: Crear repositorio en GitHub

1. Se creó un repositorio público llamado `pokedex` en [github.com](https://github.com)
2. Se configuró como **Public** para que el profesor pueda acceder y Azure pueda conectarse


Se inicializó Git y se subió el código:

```bash
git init
git config --local user.name "WilsonEHerrera"
git config --local user.email "{EMAIL_GITHUB}"
git add .
git commit -m "Codigo fuente inicial"
git branch -M main
git remote add origin https://github.com/WilsonEHerrera/pokedex.git
git push -u origin main
```

---

## Paso 5: Crear Static Web App en Azure

1. Se ingresó al portal de Azure en [portal.azure.com](https://portal.azure.com)
2. Se buscó el servicio **Static Web Apps** y se hizo clic en **Crear**

**Configuración utilizada:**

| Campo | Valor |
|-------|-------|
| Subscription | Azure for Students |
| Resource Group | rg-pokedex-prod |
| Name | swa-pokedex-portal-prod-we |
| Plan type | Free |
| Region | East US 2 |
| Source | GitHub |
| Organization | WilsonEHerrera |
| Repository | pokedex |
| Branch | main |
| Build Preset | Angular |
| App location | ./ |
| Output location | ./dist/pokedex-angular |

**Error encontrado al crear el recurso:**
```
Resource was disallowed by Azure: This policy maintains a set of best 
available regions where your subscription can deploy resources.
```

**Solución aplicada:**

La región seleccionada inicialmente no estaba disponible para la suscripción Azure for Students. Se cambió la región a **East US 2** y el recurso se creó exitosamente.

---

## Paso 6: Verificar despliegue con GitHub Actions

Azure creó automáticamente un workflow de CI/CD en `.github/workflows/`. Se verificó en la pestaña **Actions** del repositorio en GitHub.

**Error encontrado:**

El repositorio traía un workflow de test-coverage del código original del profesor que fallaba porque el entorno de CI no tiene navegador para ejecutar los tests de Angular:

```
Run test coverage failed
npm run test:cov - error
```

**Solución aplicada:**

Se eliminó el archivo `.github/workflows/test-coverage.yml` y se hizo push:

```bash
git add .
git commit -m "eliminar workflow de test coverage"
git push origin main
```

**Error al hacer push:**
```
! [rejected] main -> main (fetch first)
Updates were rejected because the remote contains work that you do not have locally.
```

**Solución aplicada:**

Azure había agregado el workflow de CI/CD directamente en GitHub, por lo que el repositorio local estaba desactualizado. Se hizo pull primero:

```bash
git pull origin main
git push origin main
```

El workflow de Azure quedó en verde confirmando el despliegue exitoso.

---

## Paso 7: Configurar headers de seguridad

Se creó el archivo `staticwebapp.config.json` en la raíz del proyecto con los headers de seguridad requeridos:

```json
{
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' data: https://fonts.gstatic.com; img-src 'self' data: https:; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co; object-src 'none'",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "no-referrer",
    "Permissions-Policy": "camera=(), microphone=(), geolocation=()"
  }
}
```

**Error encontrado después de configurar el CSP inicial:**

La aplicación presentaba errores en consola porque el CSP bloqueaba recursos externos que Angular necesita:

```
Loading stylesheet 'https://fonts.googleapis.com/...' violates CSP directive: "style-src 'self' 'unsafe-inline'"
Connecting to 'https://beta.pokeapi.co/graphql/v1beta' violates CSP directive: "connect-src 'self' https://pokeapi.co"
```

**Solución aplicada:**

Se actualizó el CSP agregando los dominios externos requeridos por la app:
- `https://fonts.googleapis.com` en `style-src` para las fuentes de Google
- `https://fonts.gstatic.com` en `font-src` para los archivos de fuente
- `https://beta.pokeapi.co` en `connect-src` para la API GraphQL de PokéAPI

Se subió el cambio:

```bash
git pull origin main
git add .
git commit -m "Añadir headers para reconocer apis externas"
git push origin main
```

---

## Paso 8: Verificar seguridad

Se escaneó la URL de la aplicación en [securityheaders.com](https://securityheaders.com):

Resultado: A

---

## Intento de mejora a calificación A+

Se intentó eliminar las directivas `'unsafe-inline'` y `'unsafe-eval'` del `script-src` para alcanzar una calificación A+ en securityheaders.com. A continuación se documenta el proceso y los resultados obtenidos.

### Paso 1: Eliminar `'unsafe-eval'`

Se quitó `'unsafe-eval'` del `script-src` y la aplicación continuó funcionando correctamente, por lo que el cambio se mantuvo. La calificación se mantuvo en A.

### Paso 2: Intentar eliminar `'unsafe-inline'`

Al quitar `'unsafe-inline'` del `script-src` la consola del navegador mostró el siguiente error:
Executing inline script violates the following Content Security Policy directive 'script-src 'self''.
Either the 'unsafe-inline' keyword, a hash ('sha256-QmIi/LDJnOEYAO9CVS/+JVvnKMiVsY6fn0FVoHnkPfg='),
or a nonce ('nonce-...') is required to enable inline execution.

Se agregó el hash proporcionado por el navegador al CSP, pero persistió un segundo error relacionado con un event handler inline generado automáticamente por Angular en el build:

```html
<link rel="stylesheet" href="styles.css" media="print" onload="this.media='all'">
```

### Paso 3: Intentar cubrir el event handler con hashes

Se calculó el hash SHA256 del event handler desde la consola del navegador:

```javascript
crypto.subtle.digest('SHA-256', new TextEncoder().encode("this.media='all'"))
  .then(buf => console.log(btoa(String.fromCharCode(...new Uint8Array(buf)))))
```

Se agregó `'unsafe-hashes'` junto con el hash calculado al CSP, pero securityheaders.com penaliza `'unsafe-hashes'` de la misma forma que `'unsafe-inline'`, por lo que la calificación bajó de A+ a A.

### Conclusión

La versión de Angular utilizada en este proyecto genera código inline en el proceso de build que no puede eliminarse sin modificar el código fuente. Esto impide alcanzar una calificación A+ sin romper el funcionamiento de la aplicación. Se optó por mantener la configuración que garantiza el correcto funcionamiento de la app con una calificación A, que cubre los headers de seguridad esenciales exigidos por el caso de estudio.

## Checklist final

- Aplicación accesible públicamente desde URL de Azure
- HTTPS activo por defecto en Azure Static Web Apps
- Sin errores 404/500 en la consola del navegador
- Headers de seguridad configurados y verificados
- Calificación A en securityheaders.com
- CI/CD funcionando con GitHub Actions
- README.md y Despliegue.md en el repositorio
