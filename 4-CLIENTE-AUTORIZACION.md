Cambios en el servidor de autorización:

Nuevo cliente en el repositorio de clientes
```
  @Bean
  public RegisteredClientRepository registeredClientRepository() {
//...
    RegisteredClient oauthClient = RegisteredClient.withId(UUID.randomUUID().toString())
        .clientId("oauth-client")
        .clientSecret("{noop}12345678910")
        .clientAuthenticationMethod(ClientAuthenticationMethod.CLIENT_SECRET_BASIC)
        .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
        .authorizationGrantType(AuthorizationGrantType.REFRESH_TOKEN)
        .redirectUri("http://localhost:8080/login/oauth2/code/oauth-client")
        .redirectUri("http://localhost:8080/authorized")
        .postLogoutRedirectUri("http://localhost:8080/logout")
        .scope(OidcScopes.OPENID)
        .scope(OidcScopes.PROFILE)
        .scope("read")
        .scope("write")
        .build();
//...
  }
```