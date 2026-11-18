# What is this

This is the code shared by the two parts of the Lua Dispatcher System for MaSzyna:

- the server (`LuaDispatcherSystem_Server`), which runs inside the simulator,
- the client (`LuaDispatcherSystem_Client`), the LOVE2D editor and previewer.

Both repositories include this one as a git submodule at `lua/core`. Change the shared code here, not in a copy.

# What is inside

| File | Contents |
| --- | --- |
| `class.lua` | Class implementation |
| `json.lua` | JSON encoder and decoder (keys are written in alphabetical order) |
| `utf8.lua` | The `utf8` library for LuaJIT, which the simulator embeds |
| `utils.lua` | General helpers; the part that draws or loads assets needs LOVE2D |
| `Time.lua`, `Utils/TimeUtils.lua` | Time of day |
| `Utils/DirUtils.lua` | The eight directions on a schematic |
| `Tile.lua`, `TileDatabase.lua` | A schematic tile and the tile catalogue |
| `Pathfinder/` | Route search on a schematic and the itineraries it produces |

# How to use it

Clone the server or the client together with its submodule:

```
git clone --recurse-submodules <repository>
```

In an existing clone:

```
git submodule update --init
```

The modules are loaded as `lua.core.<module>`, for example `require("lua.core.class")` or
`require("lua.core.Pathfinder.Pathfinder")`. The folder has to be at `lua/core`, counted from the directory Lua is started in,
because the files load each other by that path.

# What the host has to provide

The shared code does not load these itself. The server and the client each define them before the code runs.

- Globals: `_Utils` (`utils.lua`), `_Dir` (`Utils/DirUtils.lua`), `_TileDB` (a `TileDatabase`), `_Log` with `write(text, severity, weight)`,
  `_Obj` (the track, switch and semaphore states) and `_Com` (communication with the other side).
- Enums: `Speed`, `SemaphoreType`, `PathMode` and `Severity`.
- The object that owns the tiles (the server's `Schematic`, the client's `Station`) has `shortcut`, `getTile(x, y)`,
  `findSchematic(shortcut)` and `getExitTile(exitID)`.

Some functions work on one side only, because they call something only that side has:

- `Itinerary:canBeDispatched()` and `Itinerary:dispatch()` are for the server.
- `Tile:getTrackReservationState()`, `Tile:getTrackVehicleState()` and `Tile:getBlockadeState()` are for the client.

## Credits
This repository contains code from external sources:
- Class implementation from https://github.com/bncastle/love2d-tutorial/blob/Episode4/class.lua (MIT license)
- JSON decoder/encoder implementation from https://github.com/rxi/json.lua (MIT license)
- UTF-8 library from https://github.com/meepen/Lua-5.1-UTF-8 (CC0-1.0 license)
