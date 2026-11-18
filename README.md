# LiteOrd bootstrap

A ready-made [ord-litecoin](https://github.com/ynohtna92/ord-litecoin) index, so a new Litecoin ordinals indexer starts near the chain tip instead of indexing for days. It comes from [LiteOrd](https://liteord.doginals.store), The Doginals Explorer's Litecoin ordinals explorer.

## Latest: block 3,214,872

| | |
|---|---|
| Release | [bootstrap-3214872](https://github.com/thedoginalsexplorer/liteord-bootstrap/releases/tag/bootstrap-3214872) |
| Last block | 3,214,872 (`a23e839a8677bee38b83bb8c71787722755dcc65b62991020440795bd41e6850`) |
| Download | 8 parts, 13.8 GiB (40.3 GiB unpacked) |
| ord | ord-litecoin 0.20.1 (github.com/ynohtna92/ord-litecoin) |
| ord flags | `--index-runes --index-addresses` (no `--index-sats`) |
| Also included | LiteOrd's LTC-20 ledger (`liteord/ltc20.sqlite`, at block 3,214,875) |

The parts, joined in order, are one zstd-compressed tar holding `ord/index.redb` and `liteord/ltc20.sqlite`. `SHA256SUMS` lists every part's hash; `bootstrap.json` has the same plus the details above.

## Use it

**1. Download** every asset of the release into one folder:

```bash
mkdir liteord-bootstrap && cd liteord-bootstrap
curl -LO https://github.com/thedoginalsexplorer/liteord-bootstrap/releases/download/bootstrap-3214872/SHA256SUMS
for i in $(seq -f %04g 0 7); do curl -LO -C - https://github.com/thedoginalsexplorer/liteord-bootstrap/releases/download/bootstrap-3214872/liteord-bootstrap-3214872.tar.zst.$i; done
```

**2. Check** the parts (any mismatch: download that part again):

```bash
sha256sum -c SHA256SUMS
```

On Windows: `Get-FileHash <part> -Algorithm SHA256` and compare with `SHA256SUMS`.

**3. Check it's your chain:** your Litecoin node's block 3,214,872 must be `a23e839a8677bee38b83bb8c71787722755dcc65b62991020440795bd41e6850`:

```bash
litecoin-cli getblockhash 3214872
```

**4. Unpack** (needs about 40 GiB free):

```bash
cat liteord-bootstrap-3214872.tar.zst.* | zstd -d | tar -x
```

**5. Run ord-litecoin 0.20.1** with exactly these index flags, on that file. Any other flags won't match the index:

```bash
ord --chain mainnet --index ord/index.redb --index-runes --index-addresses server
```

You can also move `ord/index.redb` into ord's data directory and drop `--index`. ord carries on from block 3,214,873. The node needs `txindex=1` and `rpcserialversion=1`, which ord needs to read MWEB blocks.

`liteord/ltc20.sqlite` is only for running LiteOrd. Plain ord doesn't use it.

## License

The index is data derived from the public Litecoin chain. Use it however you like; no warranty.
