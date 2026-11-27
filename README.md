# Visual Micro third-party sources

Source archives, licences, attribution and build instructions for third-party components used by Visual Micro.

## Downloads

Download the named ZIP assets from the [2026-11-26 release](https://github.com/VisualMicro/third-party-sources/releases/tag/2026-11-26):

| Archive | Contents |
|---|---|
| VisualMicro-third-party-sources-2026-11-26.zip | AVR GDB 7.8 / Atmel 3.6.1 and Arduino toolchain build scripts; Arduino Exuberant Ctags 5.8-arduino11; ZedGraph 5.1.7; Ude sources at commit d17ab7282a419de7313ffb90be3debfea32653c2 |
| VisualMicro-TaskDialog-CPL-1.0-sources-2026-11-26.zip | Modified TaskDialog C# library, project and resources; assembly 4.0.0.70, file version 4.0.0.213; CPL 1.0 |
| VisualMicro-Tmds.MDns-LGPL-sources-2026-11-26.zip | Historical Tmds.MDns source snapshot dated 2026-11-26; assembly/file version 0.7.2.0; LGPL notices and licence texts included |

The repository's SHA256SUMS file lists the SHA-256 checksums of these three release ZIPs. Each ZIP also contains a SHA256SUMS manifest for its own contents.

GitHub's automatically generated Source code archives contain repository documents. Use the named release assets for the component source trees.

## Licences and build instructions

Each component retains its own licence. See the licence texts, attribution notices, source headers and version records inside the archives.

The component bundle includes GPL notices for GDB and ctags, LGPL notices for ZedGraph, and the upstream MPL/GPL/LGPL alternatives for Ude. The TaskDialog archive includes the CPL text and original VDialog attribution. The dated Tmds.MDns archive retains its historical LGPL notices.

See [BUILDING.md](BUILDING.md) and the build documentation inside each ZIP. The [ZedGraph replacement guide](zedgraph-library-replacement.md) describes preparing a compatible replacement for installations using ZedGraph 5.1.7.430.
