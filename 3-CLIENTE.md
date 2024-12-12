```
@Configuration
@EnableWebSecurity
public class SecurityConfig {
	@Bean
	public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
		http
				.authorizeHttpRequests((oauthHttp) -> oauthHttp
						.requestMatchers(HttpMethod.GET, "/authorized").permitAll()
						.anyRequest().authenticated())
				.oauth2Login((login) -> login.loginPage("/oauth2/authorization/oauth-client"))
				.oauth2Client(Customizer.withDefaults());
		return http.build();
	}
}
```

application.yml
```
spring:
  security:
    oauth2:
      client:
        registration:
          oauth-client:
            provider: servidor-autorizacion
            client-id: oauth-client
            client-name: oauth-client
            client-secret: 12345678910
            authorization-grant-type: authorization_code
            redirect-uri: "http://localhost:8080/authorized"
            scope: openid,profile,read,write
        provider:
          servidor-autorizacion:
            issuer-uri: http://localhost:9000
```

ClientController
```

@RestController
public class ClientController {

    @GetMapping("/hello")
    public ResponseEntity<String> hello() {
        return ResponseEntity.ok("Hello");
    }

    @GetMapping("/authorized")
    public Map<String, String> authorize(@RequestParam String code) {
        return Collections.singletonMap("authorizationCode", code);
    }

}
``` 