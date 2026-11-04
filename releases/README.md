# `releases/` — retained binary release set

On-disk retention for the platform binaries the VClaw desktop bundles. **Never committed**
(`.gitignore`d): ~216 MB / ~228 MB per darwin binary.

## Contents

```
releases/v18.16.0/
  omp-darwin-arm64      # bun build --compile, from upstream release v18.16.0
  omp-darwin-x64
  SHA256SUMS.txt        # upstream digests — the pin; after-pack.cjs verifies against these
```

## Provenance and refresh

The binaries are **upstream's own release assets** for tag `v18.16.0`
(`can1357/oh-my-pi`), not built here: `bun scripts/build-binary.ts` needs the bazel-built
natives (`pi_natives.darwin-*.node`) and refuses without them.

```bash
gh release download v18.16.0 -R can1357/oh-my-pi \
  -p 'omp-darwin-arm64' -p 'omp-darwin-x64' -p 'SHA256SUMS.txt' --clobber
```

Re-fetch from this Forgejo instance (tailnet, seconds instead of ~30 min):

```bash
curl -O https://vmaker-pi.ruffe-court.ts.net/forgejo/chatek/oh-my-pi/releases/download/v18.16.0/omp-darwin-arm64
```

## Consumers

`vclaw-desktop` (branch `vizmaster`) maps `omp-darwin-${arch}` →
`resources/harness/bin/omp` through `build.mac.extraResources`;
`scripts/release/after-pack.cjs` refuses the build if the packaged file is missing, its
sha256 differs from `SHA256SUMS.txt`, or its Mach-O slice does not match the build arch.
