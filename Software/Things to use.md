Code gen 
# 📦 3. Type & Schema Sync — Keep everything consistent

|Tool|What it syncs|
|---|---|
|**GraphQL Code Generator**|GraphQL schema → types, resolvers, clients|
|**Prisma**|Database schema → types + fluent client|
|**TypeORM / MikroORM CLI**|DB models → migrations & types|
|**Hasura**|Database → GraphQL API automatically|
|**Supabase**|Postgres → instant APIs + types|
### 🛠 4. Build / Dev Tooling — Automate the boring stuff

These tools don’t generate app code, but **save you effort**:

| Tool                    | What it does                          |
| ----------------------- | ------------------------------------- |
| **ESLint**              | Finds code issues via static analysis |
| **Prettier**            | Auto-formats code                     |
| **Husky + lint-staged** | Enforces quality on commits           |
| **Turbo / Nx**          | Monorepo build orchestration          |
| **Vite**                | Fast frontend dev server              |

# 🔐 5. Auth & Security Helpers

More tools that reduce boilerplate and protect your app:

| Tool                            | Use Case                                     |
| ------------------------------- | -------------------------------------------- |
| **Passport**                    | Auth strategies (JWT, OAuth, etc.) in NestJS |
| **Casl**                        | Authorization rules + TS safety              |
| **Grant**                       | OAuth flow helpers                           |
| **Ory / Auth0 / Supabase Auth** | Hosted auth + SDKs                           |

Other similar tools include:

➡ Validation schemas that generate types  
➡ API specs that generate clients  
➡ DB schemas that generate models and migrations  
➡ Testing mocks that reuse your types