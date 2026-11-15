# [All the Mods 10-8.6](https://www.curseforge.com/minecraft/modpacks/all-the-mods-10) server for Docker and Unraid

A Docker image that runs an **All the Mods 10** Minecraft server. At start-up it asks CurseForge for the newest server pack, installs it together with the NeoForge version the pack needs, and starts the server. When a new pack version appears, the image is rebuilt automatically, and a restarted container updates itself.

Looking for All the Mods 11? See [PaddyZockt/docker-allthemods11](https://github.com/PaddyZockt/docker-allthemods11).

Unraid users: install it from the Apps tab (Community Applications) once it is listed there, or use the template at https://github.com/PaddyZockt/unraid-templates (`templates/allthemods10.xml`).

This repository is not affiliated with the All the Mods team or CurseForge. No mod files are inside the image; the pack is downloaded when the container starts.

## Quick start

```
docker run -d --name atm10 \
  -p 25565:25565 \
  -v /path/to/atm10-data:/data \
  -e EULA=true \
  -e CURSEFORGE_API_KEY=your-key \
  paddygametv/docker-allthemods10:latest
```

The first start downloads and installs the pack, which takes a few minutes. Follow it with `docker logs -f atm10`. Later starts only start the server, or update it when CurseForge offers a newer pack.

## Requirements

* `/data` on persistent storage. The container runs as uid 99 / gid 100 (the Unraid defaults); on other systems make `/data` writable for that user.
* Port 25565/tcp.
* `EULA=true`. By setting it you accept the [Minecraft EULA](https://aka.ms/MinecraftEULA).
* `CURSEFORGE_API_KEY`: your own free key from https://console.curseforge.com. CurseForge only serves server packs to requests with a key.

## Settings

All optional. Set them as environment variables.

| Variable | Meaning | Example |
|---|---|---|
| `JVM_OPTS` | Java options (memory) | `-Xms2500m -Xmx8192m` |
| `MOTD` | Server list message | `All the Mods 10 Server` |
| `MAX_PLAYERS` | Player limit | `5` |
| `ONLINE_MODE` | `true` or `false`: check accounts with Mojang | `true` |
| `ALLOW_FLIGHT` | `true` or `false` | `true` |
| `ENABLE_WHITELIST` | `true` or `false` | `false` |
| `WHITELIST_USERS` | Comma separated names added to the whitelist | `Steve, Alex` |
| `OP_USERS` | Comma separated names that become operators | `Steve, Alex` |

## Troubleshooting

* **Container stops right away:** read the first log lines. They say what is missing (EULA, API key, download problem).
* **Permission errors on `/data`:** give uid 99 / gid 100 write access to the folder.
* **Install is incomplete or broken:** stop the container and delete `/data/.installed_server_version`. The next start installs the pack again; your world stays.
* **CurseForge refuses the download:** check that the key is valid and that you copied all of it.

## Credits

The start-up logic was rewritten from scratch; the repository started as a fork of [W3LFARe/docker-allthemods10](https://github.com/W3LFARe/docker-allthemods10), which in turn builds on [Goobaroo/docker-allthemods9](https://github.com/Goobaroo/docker-allthemods9). Thanks to both authors.
