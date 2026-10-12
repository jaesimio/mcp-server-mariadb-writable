# MCP Server for MariaDB (Writable)

A Model Context Protocol server for MariaDB that provides not only read-only access but also optional write capabilities (`INSERT`, `UPDATE`, `DELETE`). This server enables LLMs to inspect database schemas and execute SQL queries based on the permissions you grant.

This project is a fork of the original [oleander/mcp-server-mariadb](https://github.com/oleander/mcp-server-mariadb), extended to support mutation operations and granular tool control.

## Components

### Tools

The server provides several tools that can be individually enabled or disabled:

- **mariadb_query_select**
  - Execute read-only SQL queries against the connected database.
  - All queries are executed within a READ ONLY transaction.
  - Enabled by default.

- **mariadb_query_insert**
  - Execute `INSERT` queries.
  - Disabled by default.

- **mariadb_query_update**
  - Execute `UPDATE` queries.
  - Disabled by default.

- **mariadb_query_delete**
  - Execute `DELETE` queries.
  - Disabled by default.

### Resources

The server provides schema information for each table in the database:

- **Table Schemas**
  - JSON schema information for each table.
  - Includes column names and data types.
  - Automatically discovered from database metadata.

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `MARIADB_HOST` | MariaDB host address | `127.0.0.1` |
| `MARIADB_PORT` | MariaDB port | `3306` |
| `MARIADB_USER` | Database user | `root` |
| `MARIADB_PASS` | Database password | `""` |
| `MARIADB_DB` | Target database name | `""` |
| `MARIADB_CONNECTION_LIMIT` | Maximum number of connections in the pool | `1` |
| `ENABLE_MARIADB_QUERY_SELECT` | Enable the SELECT tool | `true` |
| `ENABLE_MARIADB_QUERY_INSERT` | Enable the INSERT tool | `false` |
| `ENABLE_MARIADB_QUERY_UPDATE` | Enable the UPDATE tool | `false` |
| `ENABLE_MARIADB_QUERY_DELETE` | Enable the DELETE tool | `false` |
| `LOG_LEVEL` | Logging level (`error`, `warn`, `info`, `debug`) | `info` |

## Usage with Claude Desktop

To use this server with the Claude Desktop app, add the following configuration to the `mcpServers` section of your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "mariadb-writable": {
      "command": "npx",
      "args": ["-y", "@jaesimio/mcp-server-mariadb-writable"],
      "env": {
        "MARIADB_HOST": "127.0.0.1",
        "MARIADB_PORT": "3306",
        "MARIADB_USER": "your_user",
        "MARIADB_PASS": "your_password",
        "MARIADB_DB": "your_db",
        "ENABLE_MARIADB_QUERY_INSERT": "true",
        "ENABLE_MARIADB_QUERY_UPDATE": "true"
      }
    }
  }
}
```

## Logging

All log output is handled by `winston` and is explicitly redirected to **stderr**. This ensures that the primary **stdout** stream remains dedicated to the MCP JSON-RPC protocol, preventing connection issues even when debug logs are enabled.

The log level can be set using the `LOG_LEVEL` environment variable (e.g., `debug`).

## License

This project is licensed under the MIT License. See the [LICENSE.md](LICENSE.md) file for details.
