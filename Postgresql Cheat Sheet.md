# Postgresql Cheat Sheet

Install psql:

```
brew doctor
brew update
brew install libpq
brew link --force libpq
```

Connect to DB on localhost with default credentials:

`psql -h 0.0.0.0 -U postgres`

Get names of all schemas:

`SELECT schema_name FROM information_schema.schemata;`

Get all table names in all schemas:

`\dt *.*`

Get all table names in public schema:

`\dt public.*`

If you see an error with stating: relation "any\_table\_name" does not exist

- Check that the table exists in general.
- Also check that it exists in the correct schema.