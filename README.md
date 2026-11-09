# MacPorts Port Updates

Original Portfiles from macports-ports, licensed under BSD. Modifications by <committer>.

## One-Time Setup

### 1. Create a Sparse Local Overlay

```bash
sudo mkdir -p /opt/macports-dev
sudo chown -R $(whoami) /opt/macports-dev
cd /opt/macports-dev
git clone <url> .
cd /opt/macports-dev/ports && portindex
```

### 2. Register It in sources.conf

Edit `/opt/local/etc/macports/sources.conf` and add **before** the rsync line:

```
file:///opt/macports-dev/ports/ [nosync]
```

## Adding a Missing Port

- Get it [here](https://github.com/macports/macports-ports), or create your own Portfile.
- Make sure to grab all patch files etc. as well.
- `cd /opt/macports-dev/ports && portindex`

## Updating a Port to the Current Version

- Edit `Portfile`:
  - Update `version`.
  - Remove `revision` (or set to `0`).
  - Leave checksums wrong on purpose.
  - Run `sudo port -d checksum <portname>` — this will fail and print the correct `rmd160`, `sha256`, and `size`.
  - Paste the hashes back into `Portfile`.

```sh
cd /opt/macports-dev/ports && portindex
port lint --nitpick <portname>
sudo port -d install <portname>
```

## Copy Update From Maintainer

- Copy the `Portfile`.
- Make your changes.
- Bump `revision` by 1.


```sh
cd /opt/macports-dev/ports && portindex
port lint --nitpick <portname>
sudo port -d install <portname>
```

## Gotchas

- `port` runs as user `macports` — the overlay path must be world-readable (`o+rX`).
- Re-run `portindex` after every Portfile change in the overlay.
- `port selfupdate` will **not** touch your overlay, only the rsync tree. That's the point.
- Don't forget to remove or comment out the `file://` source when you deactivate the overlay.
