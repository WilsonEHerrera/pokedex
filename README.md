# PokeDex - Despliegue en Azure Static Web Apps

Aplicación PokeDex desarrollada en Angular por Keiler Mora, desplegada en Azure Static Web Apps como parte del caso de estudio de **Pueblo Paleta Inc.**

La aplicación permite explorar diferentes especies de Pokémon, mostrando información detallada sobre sus características y habilidades, consumiendo la API RESTful [PokéAPI](https://pokeapi.co/).

---

## URL de la aplicación

[Ver aplicación en vivo](https://purple-meadow-0dce8c50f.2.azurestaticapps.net/)

## Repositorio

[Ver repositorio en GitHub](https://github.com/WilsonEHerrera/pokedex)

---

## Tecnologías utilizadas

- **Angular** - Framework de desarrollo frontend
- **PokéAPI** - API RESTful de datos Pokémon
- **Azure Static Web Apps** - Plataforma de hospedaje en la nube
- **GitHub Actions** - CI/CD automático en cada push

---

## Creación de la cuenta en Azure for Students

### Paso 1: Registro

1. Ingresa a [Azure for Students](https://azure.microsoft.com/es-es/free/students/)
2. Haz clic en **"Empezar gratis"**
3. Inicia sesión con tu cuenta institucional (correo universitario)
4. Completa el formulario de registro con la información solicitada
5. Acepta los términos y condiciones

### Paso 2: Acceso al portal

Una vez activada la cuenta, accede al portal de Azure en [portal.azure.com](https://portal.azure.com)

---

## Seguridad

La aplicación tiene configurados los siguientes headers de seguridad mediante el archivo `staticwebapp.config.json`:

| Header | Propósito |
|--------|-----------|
| `Content-Security-Policy` | Controla qué recursos puede cargar la app (previene XSS) |
| `Strict-Transport-Security` | Obliga el uso de HTTPS |
| `X-Content-Type-Options` | Evita la detección automática de tipos de archivo |
| `X-Frame-Options` | Evita que la app se muestre en iframes externos (previene clickjacking) |
| `Referrer-Policy` | Minimiza la fuga de información en cabeceras HTTP |
| `Permissions-Policy` | Restringe acceso a funcionalidades sensibles del navegador |

**Calificación obtenida en [securityheaders.com](https://securityheaders.com): A**

---

## Reflexión técnica

### ¿Qué vulnerabilidades previenen los encabezados implementados?

- **Content-Security-Policy** previene ataques XSS (Cross-Site Scripting) controlando exactamente qué recursos puede cargar y ejecutar la aplicación.
- **Strict-Transport-Security** evita ataques de tipo man-in-the-middle forzando que toda comunicación se haga por HTTPS.
- **X-Content-Type-Options** evita que el navegador intente "adivinar" el tipo de archivo, lo que podría usarse para ejecutar código malicioso disfrazado de otro tipo de archivo.
- **X-Frame-Options** impide que la app sea embebida en iframes de otros sitios, previniendo ataques de clickjacking donde un usuario cree estar haciendo clic en la app pero en realidad está interactuando con una página maliciosa.
- **Referrer-Policy** evita que se filtre información sensible de la URL al navegar a otros sitios.
- **Permissions-Policy** restringe el acceso a funcionalidades del navegador como cámara, micrófono y geolocalización, reduciendo la superficie de ataque.

### ¿Qué aprendiste sobre la relación entre despliegue y seguridad web?

Aprendí que desplegar una aplicación no termina cuando el sitio está en línea, sino que la seguridad es parte integral del proceso y debe planificarse desde el inicio. Configurar los headers HTTP correctamente es una de las primeras capas de protección que se debe implementar, y plataformas como Azure facilitan esto sin necesidad de administrar un servidor directamente. También entendí que una aplicación puede estar funcionando correctamente pero seguir siendo vulnerable si no se configuran adecuadamente las políticas de seguridad.

### ¿Qué desafíos encontraste en el proceso?

El principal desafío fue configurar correctamente el Content-Security-Policy, ya que al restringir los recursos permitidos la aplicación dejaba de cargar fuentes y de conectarse a la API de PokéAPI, por lo que fue necesario analizar los errores en la consola del navegador para identificar qué dominios externos estaban siendo bloqueados y agregarlos al CSP de forma controlada. Otro desafío fue el error de sincronización con Git cuando Azure agregó el workflow de CI/CD directamente en el repositorio remoto, lo que requirió hacer un pull antes de poder continuar con los cambios locales.

---

# Ejecutar localmente

## Pokédex Angular

[![code style: prettier](https://img.shields.io/badge/code_style-prettier-ff69b4.svg)](https://github.com/prettier/prettier)
[![codecov](https://codecov.io/gh/keilermora/pokedex-angular/branch/master/graph/badge.svg?token=9E0D28IOFT)](https://codecov.io/gh/keilermora/pokedex-angular)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)

[https://keilermora.github.io/pokedex-angular/](https://keilermora.github.io/pokedex-angular/)

La aplicación muestra el listado y el detalle de los Pokémon de las primeras 3 generaciones.

La imagen que representa un Pokémon en el listado muestra las variaciones que estos tuvieron durante las primeras versiones, desde la versión Green (1996) hasta la version Emerald (2005).

Los detalles de un Pokémon individual muestra sus estadísticas base y los registros de la Pokédex de las diferentes versiones.

El proyecto fue desarrollado usando la librería de JavaScript [Angular](https://angular.io/) para crear la interfaz de usuario, en comunicación con la Api RESTful [PokéAPI](https://pokeapi.co/).

## Requisitos mínimos

- [Nodejs](https://nodejs.org) con soporte de largo plazo (LTS).
- Un navegador web

## Ambiente de pruebas

Ejecutar en la raíz del proyecto:

```
npm start
```

## Referencias

- [Angular](https://angular.io/): One framework.
- [Angular Folder Structure](https://angular-folder-structure.readthedocs.io/en/latest/): Create a skeleton structure which is flexible for projects big or small.
- [Font Awesome](https://fontawesome.com/): The web's most popular icon set and toolkit.
- [Normalize.css](https://necolas.github.io/normalize.css/): A modern, HTML5-ready alternative to CSS resets.
- [PokéAPI](https://pokeapi.co/): The RESTful Pokémon API.
