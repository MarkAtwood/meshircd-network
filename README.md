# MeshIRCd Network

Server discovery config for the MeshIRCd federated IRC network.

## Joining

1. Run `meshircd --init` to generate your server block
2. Fork this repo
3. Add your block to `servers.json`
4. Open a PR
5. Wait for merge
6. Start your server pointing at this repo

## servers.json Format

```json
{
  "network": "MeshIRCd",
  "version": 42,
  "servers": {
    "irc.example.com": {
      "port": 6697,
      "pubkey": "ed25519:MCowBQYDK2VwAyEA...",
      "admin": "you@example.com",
      "location": "US-West"
    }
  }
}
```

## Key Generation

```bash
meshircd --init --hostname irc.yourserver.com --port 6697 --admin you@example.com
```

## Discovery URL

Point your server at:
```
https://raw.githubusercontent.com/MarkAtwood/meshircd-network/main/servers.json
```

## Leaving

Open a PR removing your server block. Peers disconnect within 5 minutes of merge.

## See Also

- [MeshIRCd](https://github.com/MarkAtwood/meshIRCd) - The server
- [DISCOVERY.md](https://github.com/MarkAtwood/meshIRCd/blob/main/DISCOVERY.md) - Full spec
