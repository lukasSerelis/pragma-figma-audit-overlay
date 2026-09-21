# Pragma Figma Audit Overlay

Turn a legacy Figma frame into a linked Pragma inventory and a readable,
toggleable migration map.

<img src="assets/inventory.svg" alt="Pragma component inventory example" width="720">

<img src="assets/overlay.svg" alt="Annotated Figma migration map example" width="720">

## Try it

Copy [`.github/skills/pragma-figma-audit-overlay`](.github/skills/pragma-figma-audit-overlay)
into the same path in your repository, then ask your agent:

```text
/pragma-figma-audit-overlay <Figma frame URL>
```

## Prerequisites

- A [skills-compatible agent](https://code.visualstudio.com/docs/copilot/customization/agent-skills).
- The [Figma remote MCP server](https://developers.figma.com/docs/figma-mcp-server/remote-server-installation/) connected with write access to the file.
- The Pragma MCP or CLI tools available to the agent for component, token, and
  implementation lookups. See [Canonical Pragma](https://github.com/canonical/pragma).
- Access to your Pragma and application Figma libraries.

The skill uses Pragma tools for the migration decisions and Figma MCP for canvas
writes. It preserves the source frame, inventories linked replacements, and adds
one toggleable annotation layer only for visible front-layer elements.

## License

[MIT](LICENSE)