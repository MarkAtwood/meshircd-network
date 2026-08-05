# MeshIRCd Network

Server discovery config for the MeshIRCd federated IRC network.

## Join the Network

### Prerequisites

- A server with a public IP and a hostname (e.g., `irc.example.com`)
- Port 6697 open (clients and server federation use the same port)
- Docker (recommended) or Go 1.21+

### Step 1: Fork this repo

Click **Fork** in the top right. You'll submit a PR later to add your server.

### Step 2: Generate your server keys

**With Docker:**
```bash
docker pull meshircd  # build from github.com/MarkAtwood/meshIRCd

docker volume create meshircd-data

docker run --rm -v meshircd-data:/data \
  -e MESHIRCD_HOSTNAME=irc.example.com \
  -e MESHIRCD_ADMIN=you@example.com \
  meshircd --init
```

**Without Docker:**
```bash
git clone https://github.com/MarkAtwood/meshIRCd
cd meshIRCd
go build -o meshircd .

./meshircd --init --hostname irc.example.com --admin you@example.com
```

This outputs a JSON block like:
```json
{
  "irc.example.com": {
    "port": 6697,
    "pubkey": "ed25519:MCowBQYDK2VwAyEA...",
    "admin": "you@example.com"
  }
}
```

**Save this output.** You'll need it for the PR.

### Step 3: Add your server to servers.json

In your fork, edit `servers.json`. Add your server block inside the `"servers"` object:

```json
{
  "network": "MeshIRCd",
  "version": 1,
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

Bump the `version` number by 1.

### Step 4: Open a Pull Request

Create a PR from your fork to this repo. Include:
- Your server hostname
- Your admin contact
- Brief description (who you are, why joining)

Example PR title: `Add irc.example.com`

### Step 5: Wait for merge

Existing operators will review. Once merged, your server is part of the network.

### Step 6: Start your server

**With Docker:**
```bash
docker run -d --name meshircd \
  --restart unless-stopped \
  -v meshircd-data:/data \
  -p 6697:6697 \
  -e MESHIRCD_HOSTNAME=irc.example.com \
  -e MESHIRCD_DISCOVERY_URL=https://raw.githubusercontent.com/MarkAtwood/meshircd-network/main/servers.json \
  meshircd
```

**Without Docker:**
```bash
./meshircd \
  --name irc.example.com \
  --discovery-url https://raw.githubusercontent.com/MarkAtwood/meshircd-network/main/servers.json
```

Within 5 minutes, peers will connect to you and you'll connect to them.

### Step 7: Connect with your IRC client

Point your client at `irc.example.com:6697` with TLS enabled. You're on the network.

## Verify It's Working

Check the logs:
```bash
docker logs meshircd
```

You should see:
```
Federation enabled, discovery URL: https://raw.githubusercontent.com/...
```

And eventually:
```
Connected to peer: irc.other-server.net
```

## Leaving the Network

1. Open a PR removing your server block from `servers.json`
2. Wait for merge
3. Peers disconnect within 5 minutes
4. Shut down your server

## Troubleshooting

**No peers connecting:**
- Check port 6697 is open (firewall, cloud security group)
- Verify your hostname resolves to your server's IP
- Check your pubkey in servers.json matches your server.key

**Clients can't connect:**
- Check port 6697 is open
- Ensure TLS is working: `openssl s_client -connect irc.example.com:6697`

**Key mismatch errors:**
- You regenerated keys after PRing your server block
- Solution: PR an update with your new pubkey, or restore your old server.key

## See Also

- [MeshIRCd](https://github.com/MarkAtwood/meshIRCd) — the server code
- [DISCOVERY.md](https://github.com/MarkAtwood/meshIRCd/blob/main/DISCOVERY.md) — full discovery spec
- [S2S.md](https://github.com/MarkAtwood/meshIRCd/blob/main/S2S.md) — federation protocol spec
