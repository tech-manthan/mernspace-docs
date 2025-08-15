## TypeORM Migration

1. Generate Migration

```bash
npm run migration:generate -- src/migration/user_token_migrations -d src/utils/data-source.ts
```

2. Run Migration

```bash
npm run migration:run -- -d src/utils/data-source.ts
```

3. Create Migration

```bash
npm run migration:create -- src/migration/migration-name
```
