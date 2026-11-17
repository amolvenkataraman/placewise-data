# placewise-data

Release hosting for the data packs of [placewise](https://github.com/amolvenkataraman/placewise),
an offline geocoder. The packs are built from public open datasets by placewise's `packbuild/`
tooling and published here as **GitHub release assets only**. No data is ever committed: this
repository's git history holds only this README, the data licence note and a `.gitignore`.

## What a release contains

Each release is one flat set of files:

| File | What it is |
|---|---|
| `index.json` | The pack index: one entry per pack with its version, schema, archive URL, SHA-256, size and source ids |
| `<name>-<version>.tar.xz` | One archive per pack (e.g. `world-2026.11.tar.xz`, `us-2026.11.tar.xz`) |
| `ATTRIBUTION.md` | The sources, licences and credit lines for the packs in this release |
| `SHA256SUMS` | SHA-256 of every file above, in `sha256sum -c` format |

- **Versions** are CalVer: `YYYY.MM` (the month the data was built), with an optional `.N` for a
  re-release in the same month (`2026.11.1`). The release tag is the data version.
- **Schema.** `index.json` and each pack carry a `schema` number (currently `1`), the format
  version described in placewise's
  [`docs/pack-format.md`](https://github.com/amolvenkataraman/placewise/blob/master/docs/pack-format.md).
  placewise refuses schemas it cannot read; adding layers or fields is not a schema change.
- **URLs in `index.json` are relative** (`"url": "world-2026.11.tar.xz"`), resolved against the
  index's own location.

`index.json` looks like this:

```json
{
  "schema": 1,
  "packs": {
    "world": {"version": "2026.11", "schema": 1, "url": "world-2026.11.tar.xz",
              "sha256": "…", "size_bytes": 10463416, "sources": ["naturalearth", "geonames", "ourairports"]}
  }
}
```

Each archive unpacks to one directory, `<name>-<version>/`, whose `pack.json` lists the SHA-256
of every file in it. placewise checks the archive hash from the index, then every file hash,
before it installs a pack.

## How placewise finds it

placewise's default index URL is the latest release's `index.json`:

```
https://github.com/amolvenkataraman/placewise-data/releases/latest/download/index.json
```

`placewise data install world` reads that index, downloads `world-<version>.tar.xz` from the same
release, verifies it and unpacks it. Because only the latest release's index is read, every
release lists every pack (an unchanged pack is uploaded again under the new release).

## Mirrors

A release is self-contained, so a mirror is just a copy of its files in one directory on any web
server or file system. Point placewise at it with any of:

- `placewise --index-url https://example.org/placewise/index.json data install world`
- the environment variable `PLACEWISE_INDEX_URL`
- `data.index_url` in `placewise.toml`
- `data.mirrors` in `placewise.toml`: a list of further index URLs (or directories, to which
  `index.json` is appended) tried in turn when the main one fails

`file://` URLs work too, for an offline copy. Integrity does not depend on where the files come
from: the hashes in `index.json` and `pack.json` are checked either way.

## Licences

The data in the packs is not placewise's own: each pack carries data under the licences of the
datasets it was built from (public domain, CC BY 4.0, government open licences and others). See
[LICENSE-DATA.md](LICENSE-DATA.md).

- placewise's
  [`ATTRIBUTION.md`](https://github.com/amolvenkataraman/placewise/blob/master/ATTRIBUTION.md)
  lists every source placewise uses, with its licence and the credit line to show.
- **Each release attaches its own `ATTRIBUTION.md`**, covering only the sources of the packs in
  that release. An app that ships or displays placewise data should show those credit lines and
  read each licence for its own obligations.

## Disclaimer

The data packs are provided "as is" and "where is", with no warranty of any kind and no legal
liability for their use. Names, boundaries and other data may be wrong or out of date and are not
an official determination of any territory's status. See [DISCLAIMER.md](DISCLAIMER.md).
