# Project Agent Notes

## Operating Rules
- Ask before high-impact changes: public APIs, database schema, auth/authz, OpenAPI generation, build/test strategy, user-facing workflows, financial/compliance logic, or behavior not documented by specs, Javadoc, README/AGENTS notes, or tests.
- Without asking, limit work to requested scope and low-risk local implementation/style details that do not affect APIs, persistence, security, Spring DI/lifecycle, generated files, external calls, stateful DB/file changes, API responses, records, or domain events.
- No global refactoring, broad reformatting, renames, or dependency updates unless directly required.
- Markdown-only changes: do not echo contents; reply with a brief summary and a `Blockers:` line (`Blockers: None` if none, otherwise list encountered blockers).

## Project Shape
- Java Spring Boot Maven modules include `<module1_name>`, `<module2_name>`; `<module1_name>` is runnable.
- OpenAPI-generated code derives from specs in `<api_module_name>/src/main/resources`; edit specs, regenerate with the existing Maven/OpenAPI workflow, and report commands/errors instead of hand-editing generated output.

## Java, Build, Test, Formatting
- Use SDKMAN with `.sdkmanrc`: check `sdk current`; run `sdk use java <version>` if it fails, is empty, or mismatches. If still mismatched, report and stop build/test.
- Package/install with tests skipped: `mvn clean install -DskipTests`. Tests: `mvn clean test`, `mvn -pl <module> test`, or `mvn -pl <module> -Dtest=<ClassName> test`.
- Do not run local `e2e-tests`; they require a deployed environment.
- For source/config changes, run `mvn fmt:format` from the affected module root, or repo root for root-level source/config (`pom.xml`, `.sdkmanrc`, `.editorconfig`, `.mvn/**`). Add source/test-only directory flags as needed. Skip for docs (`.md`, `.txt`, `.adoc`).

## Design and Implementation
- Keep classes focused and follow local Java style: obvious `var`, records for simple immutable carriers, and `StringUtils.isBlank(value)` for blank checks.
- Keep bean construction, property binding, manual DI wiring, and lifecycle orchestration out of services; use configuration/properties/runner classes and inject adapters into services.
- Prefer `@Component` with constructor injection for project-owned stateless/application singletons. Use `@Configuration`/`@Bean` for third-party/framework, named/conditional, property-driven, or factory setup; comment exceptions and `@Lazy`.
- Prefer `ApplicationRunner` over `@PostConstruct` for startup actions. Do not expose package-private collaborators through public constructors/methods; align visibility or narrow APIs where Spring/tests allow.
- Avoid unsafe `Optional.get()`, type-like or keyword variable names, and other local style deviations.

## Errors and Boundaries
- Keep sentinel/null states explicit; do not coerce `null` to sentinels unless documented.
- Translate only boundary failures documented by Javadoc, OpenAPI specs, class docs, catch blocks/exception handlers, or tests. Validate caller preconditions first; translate documented transport/I/O/runtime boundary failures; let unexpected programmer/runtime failures propagate unless the documented contract requires wrapping.
- In catch clauses, prefer `Exception` over `Throwable` unless `Error` handling is intentional; use `Throwable` in parameters/helpers only when the API semantically accepts any thrown failure.

## Tests
- Use AssertJ and existing assertion style; keep exception assertion lambdas to the throwing invocation.
- Unit tests should have one production subject; mock/fake collaborators with I/O, framework/external behavior, branching logic, or their own focused tests.
- Test public behavior; avoid new reflection tests for private methods. Prefer DI, `@Bean` overrides, `@TestConfiguration`, or profile properties over test-only constructors/backdoors.
- WireMock/integration tests should cover successful flows, spec/test-defined error paths, and boundary inputs for cross-component/I/O contracts; move detailed parsing/classification matrices to collaborator unit tests after extraction.
- Prefer explicit test wiring over package-wide `@ComponentScan`. Keep helpers private unless duplicated in same-package tests, assert or comment unused timing/sync statuses, and use meaningful exception messages.
