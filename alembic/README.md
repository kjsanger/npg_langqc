# Database migrations with [Alembic](https://alembic.sqlalchemy.org/en/latest/)

You need to set `ALEMBIC_DB_URL` to the SQLAlchemy URL for the database you
want to operate on.
The format is `driver://user:pass@host:port/dbname`.

## Creating migrations for the QC database schema

What you need:

- a database with the schema from which you want to migrate.
- the URL for that database set in `ALEMBIC_DB_URL`
- the changes already applied in [lang_qc/db/schema.py](../lang_qc/db/qc_schema.py)

Create a revision:

```alembic revision --autogenerate -m 'This is a revision description'```

We recommend to define the migration manually. Delete the content of the
`upgrade` and `downgrade` function definitions in the script generated by the
above command, define explicitly SQL statements that have to be executed and
call execution of these statements, one at a time. Executing multiple statements
in one go does not work. See examples in the [versions](./versions)
directory.

To follow a 'pure' alembic migration path (not recommended), check the
generated revision (alembic should show you the path to it), and modify it
as necessary. There is [a list of changes Alembic might not detect correctly].
This includes table/column name changes, which generate deletion and creation
of tables/columns, so these must be changed manually.

Both DDL and DML statements cam be included into the  migration. However, we
recommend that DML statements change data only for tables which perform the
role of static dictionaries.

## Running migrations

Check what is going to be executed by generating an SQL script. Use

```alembic upgrade start:end --sql```

where start and end are revision indentifiers. Your SQL script will be printed to
standard out. If you need to generate SQL to go from scratch, only specify the
end revision.

Run a migration with

```alembic upgrade revision```

where `revision` is the revision identifier. Your database will now have the
updated schema.


[a list of changes Alembic might not detect correctly]: https://alembic.sqlalchemy.org/en/latest/autogenerate.html#what-does-autogenerate-detect-and-what-does-it-not-detect
