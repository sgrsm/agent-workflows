# Project Agent Notes

## General
- Ask the user to clarify before proceeding when multiple plausible interpretations could materially change correctness, behavior, scope, safety, or edited files; otherwise state any low-risk assumption and continue.
- Keep code changes to a reasonable minimum: as few as possible, as many as necessary.
- If the user asks to create Markdown files, do not repeat the file contents in the reply. Respond with only a brief summary and note any problems or blockers encountered.

## Scope
<scope_declaration>

## Code/Test Quality Baseline
- Fix the targeted issue without introducing an equivalent/worse smell elsewhere; keep the change surface as small as correctness allows.
- Keep responsibilities cohesive by reason to change; do not replace a broad class with a broad helper/mapper.
- Keep config/wiring/lifecycle outside runtime/business code; inject non-trivial collaborators instead of constructing them inside services or hiding shared behavior in static utilities.
- Map known boundary failures; do not hide unexpected programmer/runtime bugs behind domain/transport exceptions unless the local contract requires it.
- Keep sentinel/null states explicit; do not silently coerce `null` to a sentinel unless documented.
- Prefer `Exception` over `Throwable` when `Error` handling is not intended; do not use `Throwable` as a general accumulator/helper/catch-all.
- Avoid test-only constructors/backdoors when normal dependency injection or a small config seam can provide the dependency.
- Avoid variable names equal or closely similar to type names and keywords such as `record`, `var`, `integer`, `object`.
- In unit tests, keep one production subject under test; mock/fake collaborators with non-trivial logic/I/O/framework behavior or their own focused tests.
- Do not test private methods via reflection; test public behavior or extract cohesive private logic into a collaborator.

## Code Style
- Use `var` for type inference when possible.
- Prefer `isBlank(stringVariable)` from `org.apache.commons.lang3.StringUtils` over `stringVariable == null || stringVariable.isBlank()`.
- For simple project-owned application-behavior singletons, use `@Component` with constructor injection. Do not add `@Configuration`/`@Bean` solely to instantiate such collaborators. Reserve `@Bean`/`@Configuration` for third-party/framework objects, named or conditional beans, property-driven construction, or non-trivial factory logic; document exceptions. No unexplained `@Lazy`.
- Prefer Spring `ApplicationRunner` pattern for startup validation or startup actions instead of `@PostConstruct`.
- Do not expose package-private collaborator types through public constructors or public methods. Either keep the public surface and collaborator type visibility aligned, or narrow the constructor/method visibility where Spring and tests allow it.
- Keep boundary failure translation scoped to known boundary failures. Do not catch broad `RuntimeException` across validation, request construction, transport execution, and response interpretation; validate caller preconditions before boundary translation, translate known runtime boundary failures explicitly, and let unexpected programmer/runtime failures propagate unless the local contract requires wrapping.

## Test Style
- WireMock/integration tests cover representative cross-component/I/O contracts, not every collaborator parsing/classification case.
- After extracting a collaborator, move detailed parsing/classification matrices to that collaborator's unit tests. Keep integration tests focused on representative wiring, protocol, and I/O behavior instead of duplicating exhaustive collaborator cases.
- Avoid broad test bridge configurations such as package `@ComponentScan` with exclusions. Prefer explicit test wiring/imports for the exact production classes under test.
- Keep test helpers minimal. Remove overloads, parameters, and abstractions once they are no longer used by multiple materially different cases.
- Do not ignore method return values in test synchronization/timing helpers. Prefer simpler mechanisms with explicit cleanup and clear intent.
- Use exception messages in tests that would help diagnostics: include operation/context and relevant identifiers, and avoid vague labels or internal jargon unless the invariant/concept is named and meaningful to maintainers.
- For exception assertions, prefer `assertThatExceptionOfType(...).isThrownBy(...)`.
- Keep exception assertion lambdas focused on the single invocation that may throw; move setup and helper calls outside the lambda.
- For no-exception assertions, prefer `assertThatNoException().isThrownBy(...)`.
- For AssertJ integer assertions equal to 1 or 0, prefer `.isOne()` and `.isZero()` instead of `.isEqualTo(1)` and `.isEqualTo(0)`.
- Chain assertions when appropriate: `assertThat(logLine).contains("stringValue1").doesNotContain("stringValue2")` instead of separate asserts
- When asserting exception, ensure only one exception type can be thrown in asserted block

## Build
- Use Java 11 JDK, managed by SDKMAN: `sdk use java 11.0.30-zulu`.
<build_instructions>

## Test
<test_instructions>

## Formatting
- After editing source code or configuration files, run `mvn fmt:format` from the lowest relevant directory:
    - source changes: `-DsourceDirectory=<dir> -DskipTestSourceDirectory=true`
    - test changes: `-DtestSourceDirectory=<dir> -DskipSourceDirectory=true`
- Do not run the formatter for documentation-only changes (`.md`, `.txt`, and similar files).

## Generated Code
- OpenAPI-generated code comes from `<openapi_path>; edit the specs, not generated output.

## Database schemas
<db_schemas>
