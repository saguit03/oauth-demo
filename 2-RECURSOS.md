```
@Configuration
@EnableWebSecurity
public class SecurityConfig {
        final static String issuerUri = "http://localhost:9000";

        @Bean
        public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
                http
                                .authorizeHttpRequests(authorize -> authorize
                                                .requestMatchers(HttpMethod.GET, "/resources/**")
                                                .hasAnyAuthority("SCOPE_read", "SCOPE_write")
                                                .requestMatchers(HttpMethod.POST, "/resources/**")
                                                .hasAuthority("SCOPE_write")
                                                .anyRequest().authenticated())
                                .oauth2ResourceServer((oauth2) -> oauth2.jwt(Customizer.withDefaults()));
                return http.build();
        }

        @Bean
        JwtDecoder jwtDecoder() {
                NimbusJwtDecoder jwtDecoder = (NimbusJwtDecoder) JwtDecoders.fromIssuerLocation(issuerUri);

                OAuth2TokenValidator<Jwt> withClockSkew = new DelegatingOAuth2TokenValidator<>(
                                new JwtTimestampValidator(Duration.ofSeconds(60)),
                                new JwtIssuerValidator(issuerUri));

                jwtDecoder.setJwtValidator(withClockSkew);

                return jwtDecoder;
        }

}

```

application.properties
```
spring.application.name=recursos
spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:9000
logging.level.org.springframework.security=DEBUG
server.port=8081
```

application.yml
```
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          jws-algorithms: RS512
          jwk-set-uri: http://localhost:9000
```

ResourceController
```
@RestController
@RequestMapping("/resources")
public class ResourceController {

    @GetMapping("/user")
    public ResponseEntity<String> read_user(Authentication authentication) {
        return ResponseEntity.ok("The user can read." + authentication.getName() + authentication.getAuthorities());
    }

    @PostMapping("/user")
    public ResponseEntity<String> write_user(Authentication authentication) {
        return ResponseEntity.ok("The user can write." + authentication.getName() + authentication.getAuthorities());
    }
}
``` 