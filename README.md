# 📃 Trade Cycling Paper

[![Paper 26.3+](https://img.shields.io/badge/Paper-26.3+-blue?logo=papermc&logoColor=white)](https://papermc.io/downloads/paper)
[![Modrinth](https://img.shields.io/badge/Modrinth-Trade%20Cycling-green?logo=modrinth)](https://modrinth.com/mod/trade-cycling)
[![GitHub Release](https://img.shields.io/github/v/release/vihaanvp/TradeCyclingPaper?label=Release&sort=semver)](https://github.com/vihaanvp/TradeCyclingPaper/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/H2H118OP6T)

Use the **[Trade Cycling mod by Max Henkel](https://modrinth.com/mod/trade-cycling)** on **PaperMC** servers (and forks like Purpur, Folia, etc.).

---

## ✨ What does this plugin do?

This server-side plugin enables the **Trade Cycling** functionality from the client-side mod on PaperMC servers. When a player with the Trade Cycling mod installed presses the cycle trades keybind (default: **C**) or clicks the widget in the villager trading GUI, the plugin intercepts the custom network packet and regenerates the villager's trades instantly — without needing to break/replace the workstation or wait for working hours.

> **Key Point:** This plugin does **not** add trade cycling on its own. It acts as the server-side handler for the client mod. Players **must** have the [Trade Cycling mod](https://modrinth.com/mod/trade-cycling) installed on their client to use the feature.

---

## 📋 Requirements

| Component | Version |
|-----------|---------|
| **Server** | Paper 26.3+ (or forks: Purpur, Folia, Paper forks) |
| **Client Mod** | [Trade Cycling](https://modrinth.com/mod/trade-cycling) 26.3+ (Fabric, Forge, NeoForge, Quilt) |
| **Java** | 21+ (required by Paper 26.3) |

> **Note:** Players without the mod can still join the server normally — they simply won't see the cycle trades button or be able to use the keybind.

---

## 🚀 Installation

1. **Download** the latest `TradeCyclingPaper-<version>-all.jar` from the [Releases](https://github.com/vihaanvp/TradeCyclingPaper/releases) page.
2. **Place** the JAR in your server's `plugins/` folder.
3. **Restart** the server (or use `/reload` if supported).
4. **Verify** installation with `/plugins` — you should see `TradeCycling` listed.

### Client Installation (for players)

Players who want to cycle trades must install the **Trade Cycling mod** on their client:

- **Modrinth:** [https://modrinth.com/mod/trade-cycling](https://modrinth.com/mod/trade-cycling)
- **CurseForge:** [https://www.curseforge.com/minecraft/mc-mods/trade-cycling](https://www.curseforge.com/minecraft/mc-mods/trade-cycling)
- **Supported loaders:** Fabric, Forge, NeoForge, Quilt

---

## 🎮 Usage

1. Open a villager trading GUI (right-click a villager with a profession).
2. **Press `C`** (default keybind) **or click the cycle button** in the trading GUI.
3. The villager's trades will instantly refresh with new offers.

> **Conditions for trade cycling to work:**
> - The villager must **not have been traded with before** (XP = 0)
> - The villager must **have a valid workstation** (job site)
> - The villager must **not have an active trade offer selected**

These are the same restrictions as the vanilla "refresh trades" mechanic — this plugin simply automates it.

---

## ⚙️ Configuration

This plugin has **no configuration file**. It works out of the box.

All client-side settings (keybind, button position, UI style) are configured in the **Trade Cycling mod's client config**.

---

## 🔧 Technical Details

### Network Protocol

The plugin listens for a custom payload packet on the `trade_cycling:cycle_trades` channel (matching the mod's `CycleTradesPacket`). When received:

1. Validates the player has a `MerchantMenu` open
2. Checks the villager meets cycling conditions (XP = 0, has job site, no active offer)
3. Resets the villager's trade offers via reflection
4. Invokes `Villager.updateSpecialPrices(Player)` to regenerate prices
5. Sends the new offers back to the client

### Reflection Usage

The plugin uses reflection to access internal Minecraft fields/methods that are not exposed via the Paper/Spigot API:

| Target | Field/Method | Purpose |
|--------|--------------|---------|
| `MerchantMenu.trader` | Field | Get the `Merchant` (villager) instance |
| `MerchantMenu.tradeContainer` | Field | Access the trade container for validation |
| `Villager.updateSpecialPrices(Player)` | Method | Regenerate trade prices with demand adjustments |

Since Minecraft 26.1+, Mojang provides **unobfuscated server JARs**, so these deobfuscated names are stable across versions.

### Compatibility

| Server Software | Supported |
|-----------------|-----------|
| Paper 26.3+ | ✅ |
| Purpur 26.3+ | ✅ |
| Folia 26.3+ | ✅ (region-aware) |
| Spigot/CraftBukkit | ❌ (requires Paper API) |
| Fabric/Forge servers | ❌ (this is a Paper plugin) |

---

## 🛠 Building from Source

### Prerequisites
- JDK 21+
- Git

### Build Commands

```bash
# Clone the repository
git clone https://github.com/vihaanvp/TradeCyclingPaper.git
cd TradeCyclingPaper

# Build the plugin (outputs to build/libs/)
./gradlew build

# Run a test server (requires accepting EULA)
./gradlew runServer
```

The build uses:
- **Gradle** (via wrapper)
- **Kotlin** 2.4.20
- **Paperweight UserDev** for Paper API and development environment
- **Shadow** plugin for fat JAR creation

---

## 📦 Versioning

This plugin follows the format: `1.0+<paper-version>`

| Plugin Version | Paper Version | Minecraft Version | Trade Cycling Mod |
|----------------|---------------|-------------------|-------------------|
| 1.0+26.3 | 26.3.build.+ | 26.3 (1.21.4) | 26.3+ |
| 1.0+26.2 | 26.2.build.+ | 26.2 (1.21.3) | 26.2 |

---

## 🐛 Troubleshooting

### "Plugin not loading" / "Invalid plugin.yml"
- Ensure you're using **Paper 26.3+** (not Spigot, not older Paper)
- Check server logs for detailed error messages

### "Trade cycling doesn't work"
- Verify the **client has the mod installed** (check client logs for `[Trade Cycling]` messages)
- Ensure the villager **hasn't been traded with** (XP bar is empty)
- Ensure the villager **has a valid workstation** nearby
- Check server console for any errors when the keybind is pressed

### "ClassNotFoundException" / "NoSuchFieldException" on startup
- This indicates a version mismatch. Ensure the plugin version matches your Paper version exactly.
- Re-download the correct version from Releases.

---

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Setup
- Open in IntelliJ IDEA (recommended) or VS Code
- Run `./gradlew runServer` to start a test server
- The test server runs on `localhost:25565` with EULA pre-accepted

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **Max Henkel** — Creator of the [Trade Cycling mod](https://github.com/henkelmax/trade-cycling) and the original trade cycling concept from [Easy Villagers](https://www.curseforge.com/minecraft/mc-mods/easy-villagers)
- **PaperMC Team** — For the excellent Paper server software and API
- **Sheepearrr** — Original author of this Paper plugin fork

---

## 🔗 Links

- **Plugin Releases:** [https://github.com/vihaanvp/TradeCyclingPaper/releases](https://github.com/vihaanvp/TradeCyclingPaper/releases)
- **Trade Cycling Mod (Modrinth):** [https://modrinth.com/mod/trade-cycling](https://modrinth.com/mod/trade-cycling)
- **Trade Cycling Mod (GitHub):** [https://github.com/henkelmax/trade-cycling](https://github.com/henkelmax/trade-cycling)
- **PaperMC:** [https://papermc.io](https://papermc.io)
- **Issues & Bug Reports:** [https://github.com/vihaanvp/TradeCyclingPaper/issues](https://github.com/vihaanvp/TradeCyclingPaper/issues)