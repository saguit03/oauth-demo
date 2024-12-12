# OAuth Demo Paso a Paso

Presentación: https://www.canva.com/design/DAGZAILOgfo/66ntjtxlcby11hS2LTRl6A/edit?utm_content=DAGZAILOgfo&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton

## Paso 0. Crear los proyectos

Descargarlo de GitHub: https://github.com/saguit03/oauth-demo

O... acceder a Spring Boot Initializr: https://start.spring.io/index.html

Project: Maven
Language: Java
Spring Boot: 3.4.0
Group: es.unex.aos
Artifact: {servidor/recursos/cliente}
Description: {Cualquiera}
Packaging: Jar
Java: 17
Dependencies:
- Spring Web
- Spring Boot DevTools
- Spring Security
- OAuth2 Resource Server (Solo para el servidor de recursos)
- OAuth2 Client (Solo para el cliente)
- OAuth Authorization Server (Solo para el servidor de autorización)

## Paso 1. Servidor de autorización

https://docs.spring.io/spring-authorization-server/reference/getting-started.html#defining-required-components

Leer 1-AUTORIZACION.md

### Probar que funcione el servidor

1. Acceder a https://oauthdebugger.com/ e introducir los siguientes datos

Authorize URI: http://localhost/oauth2/authorize
Redirect URI: https://oauthdebugger.com/debug
Client ID: oidc-client
Scope: profile
Response type: code

2. Enviar la petición
3. En Postman, realizar una petición POST a http://localhost:9000/oauth2/token
- Completar Authorization:
  - Basic Auth
  - Datos del cliente registrados en el paso 1
- Body
  - code (token devuelto por el debugger)
  - grant_type: authorization_code
  - redirect_uri: https://oauthdebugger.com/debug
4. Comprobar el token en jwt.io

## Paso 2. Servidor de recursos

https://docs.spring.io/spring-security/reference/servlet/oauth2/resource-server/jwt.html

Leer 2-RECURSOS.md

### Probar conexión entre ambos servidores

Pasos 1-4 igual que al probar el servidor de autorización.

5. Crear una nueva petición en Postman a la dirección: http://localhost:8081/resources/user
- Auth Type: Bearer token
- Introducir el JWT obtenido del servidor de autorización

## Paso 4. Crear un cliente de ejemplo

Leer 3-CLIENTE.md y 4-CLIENTE-AUTORIZACION.md

### Probar el cliente

Acceder a http://localhost:8080


1. Acceder a https://oauthdebugger.com/ e introducir los siguientes datos

Authorize URI: http://localhost/oauth2/authorize
Redirect URI: https://oauthdebugger.com/debug
Client ID: oidc-client
Scope: profile
Response type: code

2. Enviar la petición
3. En Postman, realizar una petición POST a http://localhost:9000/oauth2/token
- Completar Authorization:
  - Basic Auth
  - Datos del cliente registrados en el paso 1
- Body
  - code (token devuelto por el debugger)
  - grant_type: authorization_code
  - redirect_uri: https://oauthdebugger.com/debug
4. Comprobar el token en jwt.io
5. Crear una nueva petición en Postman a la dirección: http://localhost:8081/resources/user
- Auth Type: Bearer token
- Introducir el JWT obtenido del servidor de autorización
