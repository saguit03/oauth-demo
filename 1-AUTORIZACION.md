# Guía para el servidor de autorización

https://docs.spring.io/spring-authorization-server/reference/getting-started.html#defining-required-components

1. Copiar el fichero SecurityConfig.java en un paquete nuevo.
2. Eliminar los errores (como los números que se han copiado de la explicación)
3. Modificar el filtro para deshabilitar una función
```
  @Bean
  @Order(2)
  public SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http)
      throws Exception {
    http
        .authorizeHttpRequests((authorize) -> authorize
            .anyRequest().authenticated())
        .csrf(csrf -> csrf.disable())
        // Form login handles the redirect to the login page from the
        // authorization server filter chain
        .formLogin(Customizer.withDefaults());

    return http.build();
  }
```
4. Cambiar los datos del usuario (opcional)
```
  @Bean
  public UserDetailsService userDetailsService() {
    UserDetails userDetails = User.builder()
        .username("aos")
        .password("{noop}aos")
        .roles("USER")
        .build();

    return new InMemoryUserDetailsManager(userDetails);
  }
```
5. Modificar los datos del cliente registrado.
```
  @Bean
  public RegisteredClientRepository registeredClientRepository() {
    RegisteredClient oidcClient = RegisteredClient.withId(UUID.randomUUID().toString())
        .clientId("oidc-client")
        .clientSecret("{noop}secreto")
        .clientAuthenticationMethod(ClientAuthenticationMethod.CLIENT_SECRET_BASIC)
        .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
        .authorizationGrantType(AuthorizationGrantType.REFRESH_TOKEN)
        .redirectUri("https://oauthdebugger.com/debug")
        .scope(OidcScopes.OPENID)
        .scope(OidcScopes.PROFILE)
        .build();
    return new InMemoryRegisteredClientRepository(oidcClient, oauthClient);
  }

```
