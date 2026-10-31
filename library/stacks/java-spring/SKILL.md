---
name: java-spring
description: Java 21, Spring Boot, JPA with Hibernate, and Maven conventions.
---

## Versions

Use the versions in `pom.xml`. Much of the Spring code models learned from is Spring Boot 2, so these older forms keep coming back:

- `jakarta.*` imports, never `javax.*`.
- A `SecurityFilterChain` bean, never `WebSecurityConfigurerAdapter`.
- `RestClient` or an HTTP service interface, never `RestTemplate`.
- On Spring Boot 4, Jackson 3 under `tools.jackson`, never `com.fasterxml.jackson` for new code.

## Java

- Use records for request and response types and value objects. They are immutable and need no boilerplate.
- Use sealed types and switch pattern matching where they replace `instanceof` chains.

## Spring

- Inject through the constructor into `final` fields, never `@Autowired` on fields, so a class can be built in a test without Spring.
- Put `@Transactional` on public service methods called from another bean. Spring ignores it on private methods and on calls within the same class.
- Mark read-only use cases `@Transactional(readOnly = true)`, and declare `rollbackFor` when a checked exception must roll back: by default only unchecked exceptions do.
- Bind configuration to `@ConfigurationProperties` records instead of scattered `@Value` fields, so it is typed and validated at startup.
- Validate request bodies with Jakarta Bean Validation and `@Valid`.
- Build problem JSON with Spring's `ProblemDetail` in a `@RestControllerAdvice`.

## JPA and Hibernate

- Make every association lazy. Load what a use case needs with `JOIN FETCH` or `@EntityGraph`, because touching a lazy association in a loop runs one query per row.
- Set `spring.jpa.open-in-view=false`, so lazy loading cannot hide in the web layer.
- Set `spring.jpa.hibernate.ddl-auto=validate`; the schema comes only from migrations.
- Store enums with `@Enumerated(EnumType.STRING)`. With the default ordinal, reordering an enum corrupts existing rows.
- Never generate `equals`, `hashCode` or `toString` over all entity fields (for example Lombok `@Data`). They load lazy associations and break entities in sets.

## Testing

- JUnit 5 with AssertJ and Mockito, never JUnit 4.
- Test services with plain JUnit and Mockito, one layer with `@WebMvcTest` or `@DataJpaTest`, and use `@SpringBootTest` only for flows that need the whole application. Every extra context configuration slows the whole suite.
- Test repositories against the production database with Testcontainers and `@ServiceConnection`, never H2.
- Replace beans with `@MockitoBean`; `@MockBean` is deprecated.
- Don't make a test `@Transactional` when the behaviour depends on the commit. The rollback hides constraint and flush errors.
- Run mutation testing with PIT (`pitest-maven`).

## Maven

- Add dependencies that Spring Boot manages without a version, so they stay compatible with each other.
- Build and test with `./mvnw verify`.
