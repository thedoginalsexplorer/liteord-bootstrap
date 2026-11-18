# LiteOrd bootstrap

A ready-made [ord-litecoin](https://github.com/ynohtna92/ord-litecoin) index, so a new Litecoin ordinals indexer starts near the chain tip instead of indexing for days. It comes from [LiteOrd](https://liteord.doginals.store), The Doginals Explorer's Litecoin ordinals explorer.

This repo also holds the source of the ord that reads it, in [`ord-litecoin/`](ord-litecoin): ord-litecoin 0.20.1, upstream commit [`d7278a51`](https://github.com/ynohtna92/ord-litecoin/commit/d7278a512a30dcd5b81f42310cd135a9d3cc7004), unchanged. Build that and the index fits.

## Latest: block 3,214,872

| | |
|---|---|
| Release | [bootstrap-3214872](https://github.com/thedoginalsexplorer/liteord-bootstrap/releases/tag/bootstrap-3214872) |
| Last block | 3,214,872 (`a23e839a8677bee38b83bb8c71787722755dcc65b62991020440795bd41e6850`) |
| Download | 8 parts, 13.8 GiB (40.3 GiB unpacked) |
| ord | ord-litecoin 0.20.1, source in [`ord-litecoin/`](ord-litecoin) |
| ord flags | `--index-runes --index-addresses` (no `--index-sats`) |
| Also included | LiteOrd's LTC-20 ledger (`liteord/ltc20.sqlite`, at block 3,214,875) |

The parts, joined in order, are one zstd-compressed tar holding `ord/index.redb` and `liteord/ltc20.sqlite`. `SHA256SUMS` lists every part's hash; `bootstrap.json` has the same plus the details above.

## Use it

**1. Build ord** from this repo (needs [Rust](https://rustup.rs)):

```bash
git clone https://github.com/thedoginalsexplorer/liteord-bootstrap.git
cd liteord-bootstrap/ord-litecoin
cargo build --release
```

The binary is `ord-litecoin/target/release/ord` (`ord.exe` on Windows).

**2. Download** every asset of the release into one folder:

```bash
mkdir bootstrap-3214872 && cd bootstrap-3214872
curl -LO https://github.com/thedoginalsexplorer/liteord-bootstrap/releases/download/bootstrap-3214872/SHA256SUMS
for i in $(seq -f %04g 0 7); do curl -LO -C - https://github.com/thedoginalsexplorer/liteord-bootstrap/releases/download/bootstrap-3214872/liteord-bootstrap-3214872.tar.zst.$i; done
```

**3. Check** the parts (any mismatch: download that part again):

```bash
sha256sum -c SHA256SUMS
```

On Windows: `Get-FileHash <part> -Algorithm SHA256` and compare with `SHA256SUMS`.

**4. Check it's your chain:** your Litecoin node's block 3,214,872 must be `a23e839a8677bee38b83bb8c71787722755dcc65b62991020440795bd41e6850`:

```bash
litecoin-cli getblockhash 3214872
```

**5. Unpack** (needs about 40 GiB free):

```bash
cat liteord-bootstrap-3214872.tar.zst.* | zstd -d | tar -x
```

**6. Run ord** (the one built in step 1) with exactly these index flags, on that file. Any other flags won't match the index:

```bash
path/to/ord-litecoin/target/release/ord --chain mainnet --index ord/index.redb --index-runes --index-addresses server
```

You can also move `ord/index.redb` into ord's data directory and drop `--index`. ord carries on from block 3,214,873. The node needs `txindex=1` and `rpcserialversion=1`, which ord needs to read MWEB blocks.

`liteord/ltc20.sqlite` is only for running LiteOrd. Plain ord doesn't use it.

## License

The index is data derived from the public Litecoin chain. Use it however you like; no warranty.

`ord-litecoin/` is ord-litecoin by ynohtna92, based on [ord](https://github.com/ordinals/ord), under its own license, CC0 1.0 ([`ord-litecoin/LICENSE`](ord-litecoin/LICENSE)).
