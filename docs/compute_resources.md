# BAG Group Computer Resources

Author: Callum Smith, Feb 2026 (last updated: Feb 2026)

The following guide provides info on connecting to our available remote machines at the command line. A more detailed guide on connecting using VS Code is also available.

The guide also details how to navigate to several popular BAG storage disks.

---

## Available Resources

The following compute resources are available to BAG group members, listed from most commonly used to least:

| Resource | Notes |
|---|---|
| Your laptop | Personal machine |
| `uol-gen-res-03` | FoE Linux CPU server |
| `uol-gen-res-04` | BAG group-specific server |
| `viper`, `jester`, `sundown` | Legacy machines — no ongoing support |
| Aire | HPC cluster |
| JASMIN | NERC collaborative computing facility |
| Google Earth Engine | Cloud-based geospatial platform |

---

## Connecting to Remote Servers via SSH

The recommended BAG method for working on remote servers is SSH, either from the command line or from an IDE such as VS Code.

> **Note:** This approach supersedes the use of the university VPN. Connecting without the VPN gives a faster and more stable connection.

### On campus (wired connection)

If you are on a wired connection within the university network, you can connect directly:

```bash
ssh <your_username>@uol-gen-res-03.leeds.ac.uk
```

### Off campus (outside the university network)

If you are off campus, you must first jump through the university access server (`rash`):

```bash
ssh -J <your_username>@rash.leeds.ac.uk <your_username>@uol-gen-res-03.leeds.ac.uk
```

---

## Simplifying Login with an SSH Config File

You can avoid typing the full connection details every time by adding entries to your `~/.ssh/config` file. Once configured, logging in is as simple as:

```bash
ssh foec3
```

### Example `~/.ssh/config`

```
Host rash
    Hostname rash.leeds.ac.uk
    User <your_username>
    ForwardAgent yes
    ForwardX11 yes
    ForwardX11Trusted yes
    RequestTTY yes

Host foec3
    HostName uol-gen-res-03.leeds.ac.uk
    User <your_username>
    ProxyJump rash
    ForwardAgent yes
    ForwardX11Trusted yes
    RequestTTY yes

Host fcwir
    Hostname uol-gen-res-03.leeds.ac.uk
    User <your_username>
    RequestTTY force
```

Replace `<your_username>` with your university username.

- The `ProxyJump rash` line in the `foec3` block routes your connection through the `rash` access server — use this alias when off campus.
- The `fcwir` alias (FoE CPU wired) skips the proxy for a faster connection when on campus. You can name these aliases whatever you like.

> **Note:** You may need to apply for access to `rash` first. Use the request form at the bottom of the [IT remote access article](https://it.leeds.ac.uk/it?id=kb_article_view&table=kb_knowledge&sys_kb_id=c05597bffbcae6501332f843ceefdc41&spa=1).

---

## SSH Keys: Passwordless Login

Logging in can be further streamlined by setting up SSH keys, so you don't need to type your password every time.

### 1. Generate a key pair

Run the following on your **local machine** (laptop). Accept the default file location and optionally set a passphrase:

```bash
ssh-keygen -t ed25519 -C "<your_username>@leeds.ac.uk"
```

This creates two files in `~/.ssh/`:
- `id_ed25519` — your **private key** (never share this)
- `id_ed25519.pub` — your **public key** (this gets copied to remote machines)

### 2. Copy your public key to the remote server

```bash
ssh-copy-id <your_username>@uol-gen-res-03.leeds.ac.uk
```

If you are off campus and connecting via `rash`, copy the key to `rash` first, then from `rash` to the target server:

```bash
ssh-copy-id <your_username>@rash.leeds.ac.uk
ssh <your_username>@rash.leeds.ac.uk
ssh-copy-id <your_username>@uol-gen-res-03.leeds.ac.uk
```

### 3. Enable agent forwarding

With `ForwardAgent yes` already set in the SSH config (see above), your local key is forwarded through `rash` to the target server automatically. This means you only need to copy your key to `rash` and each target machine once — subsequent logins via `ProxyJump` will work without a password.

---

## Navigating to BAG Storage Disks

Once connected, you can navigate to the BAG storage disks. The path structure uses `/uolstore/` rather than the older `/nfs/` route.

**b0122** — legacy (but maintained) b drive:

```bash
cd /uolstore/Research/b/b0122/
```

**p0106** — newly acquired storage:

```bash
cd /uolstore/RIT/RITCore/Env/p0106/
```

You may not have permissions to access these drives, but if you're in BAG you can ask IT to add you as a user, most likely to p0106.
