# OAuth Demo Paso a Paso

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

Cambios respecto al SecurityConfig de la documentación de Spring Boot:

```java
  @Bean
  @Order(2)
  public SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http)
      throws Exception {
    /**
     * Se configura con una forma de inicio de sesión predeterminada
     */
    http
        .authorizeHttpRequests((authorize) -> authorize
            .anyRequest().authenticated())
        .csrf(csrf -> csrf.disable()) // Solo porque estamos en una demo, en un caso real NO se debe deshabilitar
        // Form login handles the redirect to the login page from the
        // authorization server filter chain
        .formLogin(Customizer.withDefaults());

    return http.build();
  }

  @Bean
  public UserDetailsService userDetailsService() {
    UserDetails userDetails = User.builder()
        .username("aos")
        .password("{noop}aos") // {noop} para decir que no está codificado
        .roles("USER")
        .build();

    return new InMemoryUserDetailsManager(userDetails);
  }

  @Bean
  public RegisteredClientRepository registeredClientRepository() {
    RegisteredClient oidcClient = RegisteredClient.withId(UUID.randomUUID().toString())
        .clientId("oidc-client") // Identificador del cliente
        .clientSecret("{noop}secreto")
        // Autenticación básica
        .clientAuthenticationMethod(ClientAuthenticationMethod.CLIENT_SECRET_BASIC)
        // Obtener un token de acceso del servidor de
        // autorización
        .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
        // Obtener un nuevo de acceso cuando el
        // actual ha
        // expirado, sin que el usuario vuelva a
        // introducir sus credenciales
        .authorizationGrantType(AuthorizationGrantType.REFRESH_TOKEN)
        .redirectUri("https://oauthdebugger.com/")
        .postLogoutRedirectUri("http://127.0.0.1:8080/")
        .scope(OidcScopes.OPENID)
        .scope(OidcScopes.PROFILE)
        // No queremos pedir el consentimiento del cliente en esta demo
        // .clientSettings(ClientSettings.builder().requireAuthorizationConsent(true).build())
        .build();

    return new InMemoryRegisteredClientRepository(oidcClient);
  }
```

### Probar que funcione el servidor

https://oauthdebugger.com/

http://127.0.0.1/oauth2/authorize