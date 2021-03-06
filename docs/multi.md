# Multi Backend #

The multi backend is designed for custom table structures as an alternative
to the standard [pgsql](pgsql.md) backend tables. It is intended to allow
the configuration of a custom set of tables with hopefully fewer rows and fewer
columns. This can be beneficial to queries in which some context (eg. zoom level)
could limit the number of tables that need to be queried. Addtionaly it would
allow more tables to be queried in parallel. 

## Database Layout ##
It connects to a PostgreSQL database and stores the data in one or more tables.
Each table is configured in a way similar to that of the `pgsql` backend.
That is essentially why it was named `multi` because it's basically multiple
`pgsql` backends each with its own set of options and only a single table.

## Table Configuration ##
As sample configuration may resemble the following:

    [
      {
        "name": "building",
        "type": "polygon",
        "tagtransform": "building.lua",
        "tagtransform-node-function": "nodes_proc",
        "tagtransform-way-function": "ways_proc",
        "tagtransform-relation-function": "rels_proc",
        "tagtransform-relation-member-function": "rel_members_proc",
        "tags": [
          {"name": "building", "type": "text"},
          {"name": "shop", "type": "text"},
          {"name": "amenity", "type": "text"}
        ]
      },
      ...
    ]

Note that each table has a `name` and can target a single type of geometry
by setting the `type` to one of `point`, `line` or `polygon`. `tagtransform`
is used to set the name of the lua script to be used for custom tag processing.
Within the lua script you may define several methods that will be called
when processing various tags, these can be named via 
`tagtransform-node-function`, `tagtransform-way-function`,
`tagtransform-relation-function`, and `tagtransform-relation-member-function`.
As with the normal top level options within osm2pgsql you can specify any of the
following: `tablespace-index`, `tablespace-data`, `enable-hstore`,
`enable-hstore-index`, `enable-multi`, `hstore-match-only`. Hstore colum names
may be specified via an array of strings named `hstores`. Finally standard columns
may be specified via an array of objects named `tags` with each object containing
a `name` and a postgres `type`. Note you may also set `flags` on each tag as with
the standard osm2pgsql style file. `flags` is formated exactly as in the style file
as a string of flag names seprated by commas.

## Example ##
An example based on the above is in [multi.lua](../multi.lua) and
[multi.style.json](../multi.style.json). It creates two tables, one for bus stops
and one for buildings. Some Lua processing is done to unify tagging values.

## Importing ##

See: [Importing](pgsql.md#importing).
