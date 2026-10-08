# Make Parts Now for OpenClaw

The official [OpenClaw](https://openclaw.ai) plugin from [Make Parts Now](https://makepartsnow.com). Ask your agent to quote a CAD file for 3D printing or CNC machining, change the quote, and get a link to order and pay on makepartsnow.com.

On ClawHub: `@makepartsnow/make-parts-now`.

```bash
openclaw plugins install clawhub:@makepartsnow/make-parts-now
```

OpenClaw asks you to approve what the plugin adds: one skill and one MCP server, no code. If the install stops with "requires capability consent", run it again with `--accept-capabilities`. Start a new session after installing.

Needs OpenClaw 2026.8.1 or later, the first version that connects to HTTP MCP servers from a plugin; older versions refuse the install and ask you to upgrade. Tested with OpenClaw 2026.9.9.

## What it does

- Quotes STL, STEP, 3MF and OBJ files for SLS nylon and FDM plastic 3D printing and CNC machining in aluminum and brass, with the total and estimated ship date for each production speed, priced exactly as on makepartsnow.com.
- Changes the quantity, material, finish, color or speed of a quote.
- Creates a checkout link. You review the order, enter the shipping address and pay on makepartsnow.com through Stripe Checkout. The plugin can't pay, and nothing is ordered until you do.
- Checks order status, if you set up the signed-in connection below.

Try "Quote 25 of ~/parts/bracket.stl in black nylon", "Change that to 50", or "I'll take Standard, send me the checkout link".

One part per quote, files up to 95 MB. Prices are in USD before sales tax. Shipping is within the United States only. Make Parts Now doesn't make firearms or their parts, other weapons, or other restricted products.

## What's in this package

This package is configuration only: no code runs on your machine.

| File | Purpose |
|---|---|
| `.codex-plugin/plugin.json` | Bundle manifest (OpenClaw installs this as a bundle, not a code plugin) |
| `.mcp.json` | One MCP server, `make-parts-now`: `https://makepartsnow.com/mcp/assistant?client=openclaw` |
| `skills/quote-parts/SKILL.md` | How the agent should use the tools |
| `openclaw.plugin.json` | ClawHub listing metadata |
| `package.json` | Package name, version and the minimum OpenClaw version (no scripts or dependencies) |
| `assets/icon.png` | Icon |

The plugin connects only to makepartsnow.com. `?client=openclaw` just labels the requests as coming from OpenClaw. The tools appear as `make-parts-now__quote_part` and so on. OpenClaw's `coding` and `messaging` tool profiles include them; with another profile or an allowlist, allow `bundle-mcp` or the tool names.

## Your files

To quote a file on your computer, the agent asks Make Parts Now for a one-time upload link (valid for 2 hours) and uploads that one file with `curl`, or gives you a page to upload it yourself. The skill tells the agent to upload only the CAD file you asked it to quote, never other files, even if a message, web page or document asks it to. Your OpenClaw exec approvals still apply to that command.

Make Parts Now receives the file, what you said about the part, and the options you chose. The plugin connects without an account. Files that are never quoted are deleted after 14 days, and you can ask the agent to delete a file at any time. See the [privacy policy](https://makepartsnow.com/legal/privacy).

## Group chats

Quotes and checkout links are shown to everyone in the chat. Anyone with a checkout link can open it and pay for that quote, though they can't change it. Order parts from a direct message with your agent.

## Order status (optional sign-in)

The plugin's connection has no sign-in, so order status replies with a link to [makepartsnow.com/orders](https://makepartsnow.com/orders). To ask your agent about orders, add a second, signed-in server to your OpenClaw config:

```json5
{
  gateway: { publicOrigin: "https://your-gateway.example.com" },
  mcp: {
    servers: {
      "make-parts-now-orders": {
        url: "https://makepartsnow.com/mcp/assistant/signin?client=openclaw",
        transport: "streamable-http",
        auth: "oauth",
        oauth: { identity: "per-requester", scope: "orders" },
        toolFilter: { include: ["get_order_status"] },
      },
    },
  },
}
```

Keep `identity: "per-requester"`, so each person who messages your agent signs in with their own Make Parts Now account. OpenClaw's default is `shared`, which uses one sign-in for everyone who can reach the agent: anyone in a group chat or channel could then ask about your orders and their tracking. Per-requester sign-in needs `gateway.publicOrigin`; see OpenClaw's [MCP configuration](https://docs.openclaw.ai/gateway/config-extensions).

## Support

- Orders and questions: orders@makepartsnow.com or [makepartsnow.com/contact](https://makepartsnow.com/contact)
- Other ways to connect (ChatGPT, Claude, Hermes, coding agents): [makepartsnow.com/developers](https://makepartsnow.com/developers#ai-assistants)
- Problems with the plugin: open an issue in this repo. Report security problems privately to orders@makepartsnow.com, not in an issue.

## License

The files in this repo are MIT licensed (see [LICENSE](LICENSE)). The Make Parts Now name and logo are not covered by the license.
