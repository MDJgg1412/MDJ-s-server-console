# MDJ's Server Console

A locally hosted Minecraft server control panel for Windows. It combines a Minecraft-inspired dark UI with Aero-style glass panels, while the Node.js backend handles the work that a browser cannot do by itself: running `.jar` files, managing folders, streaming logs, and sending console commands.

## Requirements

- Windows 10 or 11
- Node.js 18 or newer
- Java installed and available as `java` in your terminal
- Minecraft server jars such as Paper, Spigot, Forge, Fabric, Vanilla, Velocity, or BungeeCord

## Setup

From this project folder:

```powershell
npm.cmd install
npm.cmd start
```

Then open:

```text
http://localhost:3000
```

If PowerShell blocks `npm`, use `npm.cmd` as shown above. If Java is not on PATH, start the app with a custom Java path:

```powershell
$env:JAVA_BIN="C:\Program Files\Java\jdk-21\bin\java.exe"
npm.cmd start
```

## Example Server Creation Flow

1. Open `http://localhost:3000`.
2. Go to `Servers`.
3. Enter a server name, RAM value such as `2G`, and a server type.
4. Upload a `.jar` file, or place a reusable `.jar` in the `jars` folder and select it from the saved jar dropdown.
5. Click `Create Server`.
6. Open `Console`, select the server, and click `Start`.
7. Use the command box for commands such as `say Hello from MDJ's Server Console`.

When a server is created, the backend automatically creates:

- `servers/<server-name>/server.jar`
- `servers/<server-name>/eula.txt`
- `servers/<server-name>/server.properties`
- `servers/<server-name>/plugins`
- `servers/<server-name>/mods`
- `servers/<server-name>/config`

## Main Features

- Multi-server creation and process control
- Server deletion from the Servers tab
- World reset repair from the Servers tab, with the old world backed up first
- Start, stop, restart, and optional auto-restart on crash
- WebSocket live console with color-coded logs
- Command input with history
- Plugin and mod jar upload/delete
- Velocity and BungeeCord proxy creation and console control
- Display-only proxy/server linking
- Player kick, ban, unban, whitelist, and whitelist removal commands
- Join/leave tracking from server logs
- File browser, upload, delete, and text editor
- Local JSON config stored in `data/config.json`

## Folders

- `frontend` contains the HTML, CSS, and browser JavaScript.
- `backend` contains the Node.js server.
- `servers` is where Minecraft servers are created.
- `proxies` is where proxy instances are created.
- `jars` is a reusable jar library.
- `data` stores runtime config and temporary uploads.

## Notes

- The panel is intended for local hosting on your own Windows computer.
- Minecraft jars are not bundled. Download the server software you want to run and upload/select the jar during creation.
- Server resource usage is sampled from the Java process on Windows using PowerShell `Get-Process`.
- Player lists are based on Minecraft join/leave logs plus `banned-players.json` and `whitelist.json`.

## Port Already In Use

If `npm.cmd start` says port `3000` is already in use, another copy of the backend is already running. Stop that process, or run on a different port:

```powershell
$env:PORT=3001
npm.cmd start
```

## World Or Log File Locked

If Paper, Spigot, Forge, or Vanilla says a world file or `logs/latest.log` is locked, another Java server process is still using that server folder. Stop the old Java process before starting the same server again.

```powershell
Get-Process java,javaw -ErrorAction SilentlyContinue
```

After this update, the panel writes `.mdj-process.json` inside each server/proxy folder and blocks duplicate starts when it remembers an already-running Java process.

If a new server fails with `Overworld settings missing`, use `Servers > Reset World`. The panel moves `world`, `world_nether`, and `world_the_end` into `backups/world-reset-...`, then the next start generates a fresh world.

## Proxy Starts But Players Cannot Join

If Velocity starts and then players see `Unable to connect you to lobby`, check that every linked backend server is running on the port listed in the proxy config.

For example, if `velocity.toml` points to:

```toml
con1 = "127.0.0.1:30066"
con2 = "127.0.0.1:30067"
```

Then `con1` must be online on port `30066`, and `con2` must be online on port `30067`. A `Connection refused` line means the proxy is alive, but the backend Minecraft server is offline or using a different port.
