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
port lint --nitpick <portname>
sudo port -d install <portname>
```

## Gotchas

- `port` runs as user `macports` — the overlay path must be world-readable (`o+rX`).
- Re-run `portindex` after every Portfile change in the overlay.
- `port selfupdate` will **not** touch your overlay, only the rsync tree. That's the point.
- Don't forget to remove or comment out the `file://` source when you deactivate the overlay.

## History

One unwrapped line per entry, oldest first, new entries appended: date, full `category/port`, one of `added`, `updated`, `changed` or `removed`, then why; `added` entries say when the port can be dropped (`drop when …`), and routine updates such as checksum fixes are left out, so `rg devel/glib2 README.md` gives a port's history and `rg 'drop when' README.md` the ports to recheck after `port selfupdate`.

- 2026-03-19 devel/git-annex added: update plus a fix the official port lacked
- 2026-03-19 fuse/gocryptfs added
- 2026-08-28 fuse/gocryptfs removed: structural security concerns
- 2026-08-28 devel/git-annex updated to 10.20260717
- 2026-08-28 devel/glib2 added: official 2.88.3_1 started depending on dbus; drop when the official port can build without dbus
- 2026-11-09 devel/git-annex updated to 10.20261026
- 2026-11-09 devel/git-annex changed: no longer depends on port:ghc (the lts-24 resolver needs GHC 9.10, port:ghc is 9.14, so stack downloads its own GHC anyway)
- 2026-11-09 finance/hledger added version 1.99.6, the most recent beta
- 2026-11-09 devel/glib2 removed: official port now has a dbus variant; use -dbus
