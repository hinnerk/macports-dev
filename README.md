# MacPorts Port Update Cheatsheet

Some ports are worth managing locally. This is how I do it.

## One-time setup

### 1. Create a sparse local overlay

```bash
sudo mkdir -p /opt/macports-dev
sudo chown -R $(whoami) /opt/macports-dev
cd /opt/macports-dev
git clone <url> .
cd /opt/macports-dev/ports && portindex
```

### 2. Register it in sources.conf

Edit `/opt/local/etc/macports/sources.conf` and add **before** the rsync line:

```
file:///opt/macports-dev/ports/
```

## Add Missing Port

- Get it [here](https://github.com/macports/macports-ports) or create your own Portfile.
- Make sure to get all the patch files &c, too.
- `cd /opt/macports-dev/ports && portindex`

## Updating Port to Current Version

- Edit `Portfile`
  - Update `version`
  - Remove `revision` (or set to `0`)
  - Leave checksums wrong.
  - `sudo port -d checksum git-annex` – This will fail and print the correct `rmd160`, `sha256`, and `size`.
  - Paste hashes into `Portfile`.

```sh
$ port lint --nitpick <portname>
$ sudo port -d install <portname>
```

## Gotchas

- `port` runs as user `macports` — the overlay path must be world-readable (`o+rX`).
- Re-run `portindex` after every Portfile change in the overlay.
- `port selfupdate` will NOT touch your overlay, only the rsync tree. That's the point.
- Don't forget to remove or comment out the `file://` source when deactivating the overlay.
