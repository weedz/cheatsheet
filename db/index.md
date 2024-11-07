# Idx

## List all indexes in a table

Source: <https://stackoverflow.com/a/2213199/3834664>

```sql
-- pg_class documentation: https://www.postgresql.org/docs/current/catalog-pg-class.html
select
    t_table.relname as table_name,
    i.relname as index_name,
    array_agg(a_attr.attname) as column_names
from
    pg_class t_table
    inner join pg_index ix on ix.indrelid = t_table.oid
    inner join pg_class i on i.oid = ix.indexrelid
    inner join pg_attribute a_attr on a_attr.attrelid = t_table.oid
where
    t_table.relkind = 'r' -- from documentation: r = ordinary table, i = index, S = sequence, t = TOAST table, v = view, m = materialized view, c = composite type, f = foreign table, p = partitioned table, I = partitioned index
    and t_table.relname = '[table_name]'
    and a_attr.attnum = ANY(ix.indkey)
group by
    t_table.relname,
    i.relname
order by
    i.relname;
```
