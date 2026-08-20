![YouCam MCP for Fashion &amp; Retail](assets/banner-fashion.png)

[![CONSOLE](https://img.shields.io/badge/YOUCAM-3183FF?style=for-the-badge)](https://yce.perfectcorp.com/api-console/)
[![DOCUMENT](https://img.shields.io/badge/DOCUMENT-FF2D78?style=for-the-badge)](https://docs.perfectcorp.com/develop/mcp.md)

Official Perfect Corp [Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro) server for fashion and retail AI. This server lets MCP clients like [Claude Desktop](https://claude.ai/download), [Cursor](https://www.cursor.com), [Github Copilot](https://github.com/features/copilot), [Codex](https://openai.com/codex) and others run a complete virtual fitting room — clothing, footwear, hats, scarves, bags and fine jewellery — from a single 2D photo.

The client handles request formatting, authentication and asynchronous polling. You add one entry to a config file and start prompting.

**Server URL:** `https://mcp-api-01.makeupar.com/mcp/fashion`

---

## Quickstart with Claude Desktop

1. Get your API key from the [YouCam API Console](https://yce.perfectcorp.com/api-console/en/api-keys/).
2. Install [Node.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
3. Go to **Claude → Settings → Developer → Edit Config → `claude_desktop_config.json`** and include the following:

```json
{
  "mcpServers": {
    "youcam-fashion": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp-api-01.makeupar.com/mcp/fashion",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

4. Quit Claude Desktop completely via **File → Exit** — closing the window is not enough — then reopen it.

---

## Other MCP clients

### Cursor

1. Open **Settings → Tools & MCP → Add Custom MCP**.
2. Add the following to `mcp.json`:

```json
{
  "mcpServers": {
    "youcam-fashion": {
      "url": "https://mcp-api-01.makeupar.com/mcp/fashion",
      "type": "http",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

3. Back in **Settings → Tools & MCP**, enable the tools you want under *Installed MCP Servers*.

### Copilot in VS Code

Run `> MCP: Add Server`, choose **HTTP**, enter the server URL, name the server, and set the `Authorization` header.

Or run `> MCP: Open User Configuration` and add the following to `mcp.json`:

```json
{
  "servers": {
    "youcam-fashion": {
      "url": "https://mcp-api-01.makeupar.com/mcp/fashion",
      "type": "http",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Any other client

Any client that supports remote MCP over HTTP works with the same three values: the URL, an `Authorization` header, and the literal `Bearer ` prefix in front of your key. Configuration is static — changing the key requires a client reload.

---

## What you can build

| Use case | What the server does |
| --- | --- |
| Apparel e-commerce try-on | Overlays outfits onto a model photo — full body, upper, lower, outerwear, footwear or auto-detected |
| Jewellery and watch showrooms | Rings with finger and knuckle placement, necklaces with clavicle tracking, earrings with ear-side selection, bracelets and watches with strap alignment |
| Full-look styling | Chains garment, bag, hat and scarf try-on into one composed look |
| Textile and material preview | Applies fabric textures and prints from a template library before production |
| Campaign and catalogue imagery | Drives any try-on tool in batch from a product feed |

The full tool list with parameters is in the [MCP documentation](https://docs.perfectcorp.com/develop/mcp.md).


---

## Example usage

**YouCam API units are consumed by these tools.**

Try asking Claude:

- "Here's a model photo and a product shot of a trench coat — put the coat on the model with auto body detection"
- "Try this engagement ring on my hand photo, ring finger, left hand, and render it with soft indoor lighting"
- "Build a full look on this portrait: the silk scarf, the wide-brim hat, and the tote bag. Model is female — show each step"
- "List the fabric templates you have, then apply the three most autumnal ones to this dress so I can compare"
- "Put these five sneakers on the same foot photo so I can lay them out side by side"

---

## How tasks and file uploads work

Try-on tools are **asynchronous**. The flow your client runs on your behalf:

1. **Provide the images.** Most try-on tools need two — the person photo and the product image. `File_Upload` opens a drag-and-drop widget; `Get_Upload_API_Info` returns the endpoint for programmatic upload.
2. **Supply required parameters.** `gender` for shoes, hats, scarves and bags; body region or category for clothing.
3. **Start the task.** The tool returns a task identifier, not a finished image.
4. **Poll for the result.** `Get_Running_Task_Status` returns the current state and, once complete, the result URLs.
5. **Download the output.** Result URLs are hosted temporarily — fetch and store anything you need to keep.

> Result URLs returned by the server must be passed through unmodified. Do not rewrite, shorten or proxy them.

For a full look, chain calls in sequence — feed the output of one try-on back in as the person photo for the next. Describe the finished look and most clients will sequence it for you.

### Input image guidance

| Input | What works best |
| --- | --- |
| Person photo | Front-facing, full subject visible, even lighting, uncluttered background |
| Apparel product shot | Flat lay or ghost mannequin, single garment, plain background |
| Jewellery product shot | Isolated product on a plain or transparent background |
| Hand, wrist or ear shots | Target body part clearly in frame and unobstructed |

---

## Pricing and usage

Each tool consumes **units** from your YouCam API balance. Cost varies by feature and output count.

Ask before you build a flow — the `Get_Feature_Cost` tool answers directly:

> "What's the unit cost for each of the jewellery try-on tools?"

Balance, top-ups and usage history live in the [YouCam API Console](https://yce.perfectcorp.com/api-console/).

---

## Troubleshooting

**Server does not appear in the client.** Validate your JSON, confirm the config path against your client's documentation, and fully restart the client.

**Claude Desktop still shows nothing after editing the config.** The window was closed but the process kept running. Quit via **File → Exit**.

**`401 Unauthorized`.** Check the header reads `Bearer YOUR_API_KEY` — the `Bearer ` prefix is required. If the key was rotated, update it and reload the client.

**Tools are listed but every call fails.** Your network cannot reach `mcp-api-01.makeupar.com`. Check firewall, VPN and proxy rules for that host.

**Call rejected with a missing parameter.** `gender` was not supplied for a shoe, hat, scarf or bag try-on. It has no default.

**The garment renders misaligned or only partially.** The person photo is angled, cropped or occluded. Use a front-facing shot with the relevant body region fully visible.

**Visible edge artefacts around the product.** The product image has a busy or textured background. Supply it on a plain or transparent background instead.

**A task never finishes.** Call `Get_Running_Task_Status` again with the task ID before assuming failure. If it errors, an input likely failed validation — re-upload cleaner images.

**Insufficient units.** Check usage and top up in the [YouCam API Console](https://yce.perfectcorp.com/api-console/).

---

## Support

Questions, bug reports and feature requests: [YouCamOnlineEditor_API@perfectcorp.com](mailto:YouCamOnlineEditor_API@perfectcorp.com)

When reporting a problem, include your MCP client and version, the tool that failed, and the task ID if one was returned.

---

© Perfect Corp. YouCam is a trademark of Perfect Corp.
