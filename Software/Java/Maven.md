# Your Maven learning roadmap

We're going to do it in this order:

### Part 1 — Core mental model

**We're here.**

- Maven
- POM
- lifecycle
- phase
- plugin
- goal
- `target`

### Part 2 — Dependencies

We'll understand:

```
<dependency>
    <groupId>...</groupId>
    <artifactId>...</artifactId>
    <version>...</version>
</dependency>
```

Then:

```
Maven Central
      ↓
Local repository ~/.m2
      ↓
Project
```

And **transitive dependencies**.

### Part 3 — Lifecycle deeply

We'll actually watch:

```
mvn validate
mvn compile
mvn test
mvn package
mvn verify
mvn install
```

and understand exactly what changes.

### Part 4 — Plugins

We'll learn:

```
plugin
goal
configuration
execution
phase binding
```

This will make your `pom.xml` stop looking like XML garbage.

### Part 5 — JARs

We'll understand:

```
.class
   ↓
JAR
```

and exactly what:

```
mvn package
```

does.

### Part 6 — Local Maven repository

We'll understand:

```
~/.m2/repository
```

and how Maven finds dependencies.

### Part 7 — Multi-module Maven

Eventually:

```
parent
 ├── module-a
 ├── module-b
 └── module-c
```

This becomes very useful in real projects.

```
validate
   ↓
compile
   ↓
test
   ↓
package
   ↓
verify
   ↓
install
   ↓
deploy
```

Lifecycle phase, plugins goal. 
```
             LIFECYCLE
                 │
                 ↓
             compile
              PHASE
                 │
        ┌────────┴────────┐
        ↓                 ↓
resources:resources   compiler:compile
      GOAL                 GOAL
        │                   │
        ↓                   ↓
 copy resources        compile Java
```
Mven concept
```
                    pom.xml
                       │
                       ↓
                     Maven
                       │
                       ↓
                  LIFECYCLE
                       │
             ┌─────────┴─────────┐
             ↓                   ↓
          PHASES              ...
             │
             ↓
          compile
             │
             ↓
       plugin goals
        /         \
       ↓           ↓
 resources      compiler
    goal          goal
                   │
                   ↓
                .class
                   │
                   ↓
                target/
```

```
                    pom.xml
                       │
                       ↓
                     Maven
                       │
                       ↓
                  LIFECYCLE
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      compile         test          package
       PHASE          PHASE           PHASE
        │              │               │
        ↓              ↓               ↓
   plugin goals   plugin goals    plugin goals
        │              │               │
        ↓              ↓               ↓
      actual work   actual work     actual work
        │
        ↓
     target/
```

# Maven Mastery Guide (Before Spring)

Goal: understand Maven deeply enough that Spring/Spring Boot's `pom.xml` never feels like magic.

---

## 1. Why Maven Exists (the mental model)

Before Maven, Java projects manually managed:

- Where to download jars from
- Which jar version worked with which other jar
- How to compile, test, package, deploy — every team did it differently

Maven fixes this with **convention over configuration**: a standard project structure, a standard build lifecycle, and a standard way to declare/download dependencies.

Think of Maven as **three things bundled together**:

1. A **build tool** (compiles, tests, packages your code)
2. A **dependency manager** (downloads jars + their transitive jars)
3. A **project description format** (pom.xml — metadata about your project)

---

## 2. Standard Directory Structure (Convention over Configuration)

```
my-app/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/          → your source code
│   │   ├── resources/     → config files, properties, xml (non-java)
│   │   └── webapp/        → for web apps (WAR projects)
│   └── test/
│       ├── java/          → test source code
│       └── resources/     → test config
└── target/                → generated output (compiled classes, jar/war, reports)
```

**Quirk:** If you don't follow this structure, Maven doesn't just "not work" — you have to override it with explicit `<build><sourceDirectory>` config, fighting the tool's whole philosophy. Spring Boot assumes this structure blindly.

---

## 3. POM (Project Object Model) — pom.xml

This is Maven's brain. Core things every pom.xml has:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <!-- Coordinates: uniquely identify this project/artifact -->
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging> <!-- jar, war, pom, ear -->

    <dependencies>...</dependencies>
    <build>...</build>
    <properties>...</properties>
</project>
```

### GAV Coordinates (Group:Artifact:Version)

This is THE most important concept — every dependency, every artifact, everything in Maven is identified by:

- **groupId** — organization/project namespace (reverse domain style: `org.springframework`)
- **artifactId** — the specific project/module (`spring-core`)
- **version** — which version

Together `groupId:artifactId:version` (GAV) = a unique coordinate to a jar in a repository.

**Interview Q: What is GAV in Maven?** → Group, Artifact, Version — the three-part coordinate that uniquely identifies any Maven artifact in a repository.

---

## 4. Build Lifecycle, Phases, Goals — (the #1 most-tested concept)

This trips up almost everyone. Get this crystal clear.

### Three separate lifecycles (independent of each other):

1. **default** — the real build (compile, test, package, deploy)
2. **clean** — deletes `target/`
3. **site** — generates project documentation site

### The `default` lifecycle — a sequence of PHASES:

```
validate → compile → test → package → verify → install → deploy
```

Key rule: **running a phase runs every phase before it too.** So `mvn install` = validate + compile + test + package + verify + install, all in order.

|Phase|What it does|
|---|---|
|validate|checks project is correct, all info available|
|compile|compiles main source code|
|test|runs unit tests (via Surefire plugin)|
|package|bundles compiled code into jar/war|
|verify|runs checks on integration test results|
|install|installs artifact into your **local repo** (`~/.m2`) so other local projects can use it|
|deploy|pushes final artifact to a **remote repo** (Nexus/Artifactory) for others to use|

### Phases vs Goals (the confusing part)

- A **goal** is a single, specific task (e.g., `compiler:compile`, `surefire:test`)
- A **phase** is a named step in the lifecycle
- **Plugins bind their goals to phases.** E.g., the compiler plugin's `compile` goal is bound to the `compile` phase.
- You _can_ run a goal directly without a phase: `mvn compiler:compile`
- Format for direct goal execution: `mvn pluginPrefix:goal`

**Interview Q: Difference between a Maven phase and a goal?** → A phase is a stage in the build lifecycle (ordered, sequential). A goal is a specific task performed by a plugin. Phases execute goals that are bound to them.

**Interview Q: What happens when you run `mvn package`?** → Maven executes every phase from `validate` up to and including `package`, running whatever plugin goals are bound to each phase along the way.

**Quirk:** `mvn test` does NOT package your app. `mvn install` does NOT deploy to a remote repo. People confuse `install` and `deploy` constantly — install = local machine only, deploy = shared/remote repo.

---

## 5. Dependencies

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>6.1.0</version>
        <scope>compile</scope>
    </dependency>
</dependencies>
```

### Dependency Scopes (heavily interviewed)

|Scope|Available at compile?|Available at test?|Packaged in final jar/war?|Example use|
|---|---|---|---|---|
|**compile** (default)|yes|yes|yes|normal libs|
|**provided**|yes|yes|**no**|servlet-api (container provides it)|
|**runtime**|no|yes|yes|JDBC driver|
|**test**|no|yes|no|JUnit, Mockito|
|**system**|yes|yes|no|local jar not in any repo (legacy, avoid)|
|**import**|—|—|—|only for `<dependencyManagement>` with pom packaging|

**Interview Q: Difference between `provided` and `compile` scope?** → Both are available during compilation, but `provided` is excluded from the final packaged artifact because the runtime environment (e.g., app server) is expected to supply it.

### Transitive Dependencies

If A depends on B, and B depends on C, your project automatically gets C too — you never declared C directly. This is transitive dependency resolution.

**Quirk — Dependency Mediation (version conflicts):** If two different paths bring in different versions of the same artifact, Maven picks the version that is **"nearest" in the dependency tree** (shortest path from your project). This is called **dependency mediation**, and it can silently give you a version you didn't expect.

Fix/inspect with:

```
mvn dependency:tree
```

This is one of the most useful commands you'll ever run — memorize it.

### Excluding a transitive dependency

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>libA</artifactId>
    <exclusions>
        <exclusion>
            <groupId>com.unwanted</groupId>
            <artifactId>badlib</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### `<dependencyManagement>` vs `<dependencies>` (very commonly asked, and this is EXACTLY how Spring Boot's BOM works)

- `<dependencies>` → actually adds the dependency to your build.
- `<dependencyManagement>` → does **not** add anything by itself. It only declares "if this artifact is used anywhere (by you or a child module), use THIS version." It centralizes version numbers without forcing inclusion.

This is precisely what `spring-boot-dependencies` BOM does — it's a giant `<dependencyManagement>` block. That's why in Spring Boot you can add a dependency _without_ specifying a version — the parent POM's dependencyManagement already pins it.

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>3.3.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

**Interview Q: How does Spring Boot let you omit versions for dependencies?** → Via BOM (Bill of Materials) import in `dependencyManagement`, inherited from `spring-boot-starter-parent` or imported directly — it pins compatible versions centrally.

---

## 6. Repositories

Three types, resolved in a specific order:

1. **Local repository** — `~/.m2/repository` on your machine. First place Maven checks.
2. **Central repository** — Maven Central (default remote repo, public, huge).
3. **Remote/private repository** — company's own repo manager (Nexus, Artifactory, JFrog) for internal/private artifacts.

**Resolution order:** local → then whatever remote repos are configured (central by default) → downloads and caches into local repo for next time.

**Quirk:** If a corrupted or partial download happens, Maven sometimes leaves a `.lastUpdated` file and silently fails to redownload. Fix: delete the specific artifact folder from `~/.m2/repository/...` and rebuild.

Configuring a private repo:

```xml
<repositories>
    <repository>
        <id>company-repo</id>
        <url>https://nexus.company.com/repository/maven-public/</url>
    </repository>
</repositories>
```

---

## 7. Plugins

Everything Maven actually _does_ is performed by plugins — even compiling is a plugin (`maven-compiler-plugin`). Maven core itself just orchestrates the lifecycle.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.13.0</version>
            <configuration>
                <source>17</source>
                <target>17</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

Common plugins you'll meet constantly in Spring projects:

- `maven-compiler-plugin` — Java version / compile options
- `maven-surefire-plugin` — runs unit tests during `test` phase
- `maven-failsafe-plugin` — runs integration tests during `verify` phase
- `spring-boot-maven-plugin` — repackages jar into an executable "fat jar" with embedded server, adds `mvn spring-boot:run`
- `maven-jar-plugin` / `maven-war-plugin` — control packaging
- `maven-shade-plugin` / `maven-assembly-plugin` — build "uber jars" bundling all dependencies together

**Interview Q: What does `spring-boot-maven-plugin` actually do?** → It repackages the normal jar Maven builds into an executable "fat jar" containing all dependencies and an embedded Tomcat/Jetty, and adds goals like `spring-boot:run` and `spring-boot:build-image`.

---

## 8. Parent POM & Inheritance

A `pom.xml` can inherit from a parent POM — shared config, dependency versions, plugin versions, properties.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.0</version>
</parent>
```

`spring-boot-starter-parent` gives you: default Java version, UTF-8 encoding, dependency version management (via its own parent, `spring-boot-dependencies`), and default plugin configuration — all inherited so your own pom.xml stays tiny.

**Quirk:** You can only have ONE parent (single inheritance, just like Java classes). If you need to inherit dependency management from Spring Boot but already have a different parent, you use the **BOM-import trick** shown above instead of `<parent>`.

---

## 9. Multi-Module Projects

A parent project with `packaging=pom` that aggregates sub-modules:

```xml
<packaging>pom</packaging>
<modules>
    <module>service-api</module>
    <module>service-impl</module>
    <module>web-app</module>
</modules>
```

Running `mvn install` from the parent builds all modules in the correct dependency order automatically. This is extremely common in real Spring microservice repos (e.g., separate modules for `api`, `core`, `web`).

**Interview Q: What's the difference between a parent POM and an aggregator POM?** → A **parent POM** is about inheritance (child pom uses `<parent>` to inherit config/versions). An **aggregator POM** is about building multiple modules together (`<modules>` + `packaging=pom`). Often the same POM does both jobs at once, but conceptually they're different mechanisms.

---

## 10. Properties & Variable Substitution

```xml
<properties>
    <java.version>17</java.version>
    <spring.version>6.1.0</spring.version>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
</properties>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring.version}</version>
</dependency>
```

You'll see `${project.version}`, `${project.build.directory}` etc. — built-in properties referring to POM elements.

---

## 11. Profiles

Different build configs for different environments (dev/test/prod), activated conditionally.

```xml
<profiles>
    <profile>
        <id>dev</id>
        <activation><activeByDefault>true</activeByDefault></activation>
        <properties><env>development</env></properties>
    </profile>
    <profile>
        <id>prod</id>
        <properties><env>production</env></properties>
    </profile>
</profiles>
```

Activate with: `mvn install -Pprod`

---

## 12. Effective POM

Every pom.xml you write is actually incomplete — it's merged with:

- its parent POM(s), all the way up to the "Super POM" (Maven's built-in root default)
- profile activations
- properties

The fully resolved, merged result is called the **Effective POM**. Command:

```
mvn help:effective-pom
```

Run this on any Spring Boot project — it's eye-opening to see how much config `spring-boot-starter-parent` silently injects.

---

## 13. Essential Commands Cheat Sheet

|Command|What it does|
|---|---|
|`mvn compile`|compile source|
|`mvn test`|run unit tests|
|`mvn package`|build jar/war|
|`mvn install`|package + put in local `~/.m2` repo|
|`mvn clean`|delete `target/`|
|`mvn clean install`|most common combo — fresh full build|
|`mvn dependency:tree`|show full dependency tree + conflicts|
|`mvn dependency:analyze`|find unused/undeclared dependencies|
|`mvn help:effective-pom`|see fully resolved pom|
|`mvn -X`|debug/verbose output|
|`mvn -o`|offline mode (use only local repo cache)|
|`mvn -DskipTests package`|package without running tests|
|`mvn versions:display-dependency-updates`|check for newer dependency versions|

---

## 14. Common Quirks & Gotchas (things that bite beginners)

1. **`-DskipTests` vs `-Dmaven.test.skip=true`**: `skipTests` still compiles tests, just doesn't run them. `maven.test.skip=true` skips compiling AND running tests entirely.
2. **Snapshot vs Release versions**: A version ending in `-SNAPSHOT` (e.g., `1.0.0-SNAPSHOT`) is a mutable "work in progress" build — Maven will re-check for updates to it constantly. A release version (`1.0.0`) is immutable and permanently cached once downloaded. Never deploy `-SNAPSHOT` to production.
3. **"Nearest wins" for transitive conflicts** can silently pick an older/incompatible version — always verify with `dependency:tree` when something behaves weirdly with Spring beans not loading, `NoSuchMethodError`, etc. This is THE most common real-world Maven+Spring bug.
4. **Local repo corruption**: half-downloaded artifacts leave `_remote.repositories` or `.lastUpdated` marker files causing "artifact could not be resolved" errors even after fixing network. Delete the folder in `~/.m2` and retry.
5. **`provided` scope confusion** — forgetting that `provided` dependencies won't be in your runnable jar, causing `ClassNotFoundException` at runtime even though it compiled fine.
6. **Plugin execution order** — plugins bound to the same phase execute in the order they're _declared_ in the pom, which matters for things like code generation before compile.
7. **Multiple `pom.xml` GAV clashes** — two dependencies with same artifactId but different groupId are NOT the same thing to Maven; a common trap when migrating old libraries.
8. **BOM import must use `<scope>import</scope>` and `<type>pom</type>`** — forgetting `type=pom` is a very common copy-paste error when setting up Spring Boot BOMs manually.
9. **Wrapper (`mvnw`/`mvnw.cmd`)**: Spring Boot projects ship a Maven Wrapper so everyone builds with the exact same Maven version without installing Maven globally. Always use `./mvnw` instead of `mvn` in these repos.

---

## 15. Interview Question Bank (rapid fire)

1. What is Maven and why do we use it? → dependency management + standardized, lifecycle-driven build.
2. What is the local repository and where is it located? → `~/.m2/repository`; caches downloaded/installed artifacts.
3. Explain the Maven build lifecycle. → validate → compile → test → package → verify → install → deploy.
4. Difference between a plugin goal and a lifecycle phase? → goal = concrete task from a plugin; phase = named ordered step; goals bind to phases.
5. What is a POM? → the XML file describing project metadata, dependencies, plugins, build config.
6. What are dependency scopes? Name them. → compile, provided, runtime, test, system, import.
7. What's a transitive dependency? → a dependency of your dependency, pulled in automatically.
8. How does Maven resolve version conflicts in transitive dependencies? → "nearest definition wins" (dependency mediation); can also be forced via `dependencyManagement`.
9. Difference between `dependencies` and `dependencyManagement`? → management declares versions/scopes centrally without adding the dependency; dependencies actually includes it.
10. What is a BOM? → a pom with `packaging=pom` used purely to centralize/import version numbers via `dependencyManagement`.
11. Difference between `mvn install` and `mvn deploy`? → install = local `.m2` repo only; deploy = pushes to a remote/shared repository.
12. What's a SNAPSHOT version? → a mutable, in-development version that Maven re-resolves/updates rather than caching permanently.
13. What is the Super POM? → Maven's implicit built-in parent POM every project inherits defaults from.
14. What is the Effective POM? → the fully merged/resolved POM after combining your pom.xml with parent(s), profiles, and defaults.
15. What's a multi-module (aggregator) project? → a parent `pom` packaging project listing child `<modules>`, built together in dependency order.
16. How do you exclude a transitive dependency? → `<exclusions>` block inside the `<dependency>`.
17. What does `mvn dependency:tree` do and why is it important? → shows resolved dependency graph including versions chosen after conflict resolution — critical for debugging version clashes.
18. What is the Maven Wrapper (`mvnw`)? → a script + jar checked into the repo that downloads/uses a pinned Maven version, so builds are reproducible without a global Maven install.
19. How does Spring Boot let dependencies be declared without a version? → via inherited/imported `dependencyManagement` from `spring-boot-starter-parent` / `spring-boot-dependencies` BOM.
20. What plugin turns a Spring Boot app into an executable jar, and what does it do? → `spring-boot-maven-plugin`; repackages into a fat/executable jar with embedded server and adds `spring-boot:run`.

---

## 16. Suggested Order to Actually Learn This

1. Build a plain Java project by hand with just `pom.xml`, no Spring — add one dependency (e.g., `commons-lang3`), run `mvn compile`, `mvn test`, `mvn package`.
2. Run `mvn dependency:tree` and `mvn help:effective-pom` on it. Read the output slowly.
3. Add a second dependency that transitively conflicts with something — observe dependency mediation.
4. Create a 2-module multi-module project (e.g., `core` + `app`) and build both together.
5. Add a `<parent>` and move shared config there.
6. Only THEN start a Spring Boot project and open its `pom.xml` — you'll recognize every single piece: the parent, the BOM import, the starters (which are just dependency artifacts with transitive deps bundled), and `spring-boot-maven-plugin`.

Once you're comfortable with all of the above, "Spring Boot starters" will stop looking magical — a starter like `spring-boot-starter-web` is just a regular Maven artifact whose sole job is to transitively pull in a curated set of other dependencies (Tomcat, Jackson, Spring MVC, etc.).

Dependency(needed by our app to run it) vs plugins(needed by maven to compile the program)

```
Read pom.xml
     ↓
Find dependencies
     ↓
Resolve dependencies
     ↓
Download missing dependencies
     ↓
Compile application
     ↓
Compile tests
     ↓
Run tests
```

```
1. Dependencies                 ← you're here
       ↓
2. Dependency scopes
       ↓
3. Transitive dependencies
       ↓
4. Dependency conflicts
       ↓
5. Maven repositories
       ↓
6. `mvn dependency:tree`
       ↓
7. POM inheritance
       ↓
8. Parent POM
       ↓
9. dependencyManagement
       ↓
10. Multi-module Maven projects
```

Yep. You've got the **core Maven engine** now. Don't spend days on Maven. You need enough depth to be dangerous/useful, then move on.

## What you should learn before Spring Boot

I'd do it in this order:

### 1. Maven lifecycle — ✅ mostly done

You understand:

```text
lifecycle
   ↓
phase
   ↓
plugin goal
```

And:

```bash
mvn compile
mvn test
mvn package
```

You understand why `mvn test` also compiles first.

**Done enough.**

---

### 2. Dependencies — 🔥 next

This is probably the **most important Maven topic** for Spring Boot.

You'll learn this:

```xml
<dependencies>

    <dependency>
        <groupId>...</groupId>
        <artifactId>...</artifactId>
        <version>...</version>
    </dependency>

</dependencies>
```

And understand:

```text
Your project
     │
     ↓
dependency
     │
     ↓
Maven repository
     │
     ↓
download JAR
     │
     ↓
~/.m2/repository
     │
     ↓
your project can use it
```

Then we'll learn:

- `groupId`
    
- `artifactId`
    
- `version`
    
- Maven Central
    
- local repository
    
- transitive dependencies
    
- dependency conflicts
    
- dependency scopes (`compile`, `test`, `provided`, etc.)
    

**This is essential.**

---

### 3. `package` and JARs

You'll run:

```bash
mvn package
```

and understand:

```text
.java
  ↓
.class
  ↓
JAR
```

And understand what that JAR actually is.

Then:

```bash
java -jar ...
```

will make much more sense later.

---

### 4. `clean`, `package`, `install`

Understand the difference:

```bash
mvn clean
```

```bash
mvn package
```

```bash
mvn install
```

Especially:

```text
package
   ↓
creates artifact for this project

install
   ↓
creates artifact
   ↓
puts it into ~/.m2/repository
```

That `install` concept is important.

---

### 5. Plugins — deeper, but not TOO deep

You've already understood:

```text
plugin
  ↓
goal
```

We'll learn just enough about:

```xml
<build>
    <plugins>
        ...
    </plugins>
</build>
```

and why a project might explicitly configure a plugin.

You **do not** need to become a Maven plugin developer.

---

### 6. POM inheritance

This is important because real-world POMs can look crazy.

You'll eventually see:

```xml
<parent>
    ...
</parent>
```

and things inherited from a parent POM.

You'll understand:

```text
Parent POM
    ↓
Child POM
    ↓
your project
```

This becomes **very important in Spring Boot projects**.

---

# Then you're done with Maven.

Seriously.

You don't need to know every Maven feature before learning Spring Boot.

Your Maven knowledge should eventually feel like:

> "I see a `pom.xml`, I know what I'm looking at. I know where dependencies come from, how Maven builds the project, what plugins are doing, what lifecycle phases mean, and what `package/install` do."

That's enough.

---

# 🚀 Then the path to Spring Boot

Since you said you know basic Java, I'd take this route:

```text
                 Java basics
                     │
                     ▼
                  Maven
                 ↙     ↘
        dependencies   lifecycle
                     │
                     ▼
               Spring Framework
                     │
          ┌──────────┼──────────┐
          ↓          ↓          ↓
        IoC/DI    Beans      Context
          │
          ▼
                 Spring Boot
                     │
          ┌──────────┼───────────┐
          ↓          ↓           ↓
      REST API    Database     Config
          │          │           │
          ↓          ↓           ↓
     Controllers   JPA/JDBC   application.yml
          │          │
          └──────────┼──────────┘
                     ↓
               Real application
```

But there's an important thing:

## Don't learn "Spring" and "Spring Boot" as random annotations.

I want you to understand the underlying concepts.

For example, instead of:

> "What's `@Autowired`?"

We'll understand:

```text
What is an object?
      ↓
Who creates the object?
      ↓
What is dependency injection?
      ↓
What is a Spring Bean?
      ↓
Who manages the Bean?
      ↓
What is ApplicationContext?
      ↓
Now what does @Autowired actually do?
```

Then the annotations make sense instead of being magic.

---

# Your immediate path

So I suggest:

### **Maven — 2 more lessons**

**Lesson A — Dependencies**

We'll create a tiny dependency ourselves and understand:

```text
groupId
artifactId
version
repository
JAR
~/.m2
transitive dependency
```

**Lesson B — Package/install + POM inheritance**

We'll understand:

```text
mvn package
mvn install
parent POM
child POM
```

Then:

# 🎯 Move to Spring

And we'll start with:

**Spring Core → IoC → Dependency Injection → Beans → ApplicationContext**

**before** jumping into REST controllers.

---

## The big picture

You don't need:

```text
Maven
 ↓
learn every single Maven feature
 ↓
finally Spring Boot
```

Instead:

```text
           Maven
        ┌────┴────┐
        ↓         ↓
   lifecycle   dependencies
        │         │
        └────┬────┘
             ↓
        "I understand
        how Java projects
        are built."
             │
             ↓
        Spring Core
             ↓
       Spring Boot
             ↓
       Build real APIs
```

**So next: Dependencies.** That's the one Maven topic I'd prioritize heavily before moving on.