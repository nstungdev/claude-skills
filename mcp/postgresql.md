# Adding a PostgreSQL MCP Server

## Prerequisites

- Docker must be installed and running, since the MCP server is launched via a Docker container.

## Setup

Run the following command in your regular terminal (**not** inside the Claude terminal):

```shell
claude mcp add <name> --scope project -- docker run -i --rm -e DATABASE_URI crystaldba/postgres-mcp --access-mode=unrestricted
```

Replace `<name>` with an identifier for the server (e.g. `postgres`).

## Reference

- See the [postgres-mcp repository](https://github.com/crystaldba/postgres-mcp) for more details.
- See the [Claude Code MCP quickstart](https://code.claude.com/docs/en/mcp-quickstart) for general MCP setup guidance.
