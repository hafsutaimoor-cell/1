# references/pitfalls.md — STUB (real content to be added)

The failure catalogue — every entry is a failure that produced a broken artifact while looking completely healthy. Read before building.

Status: this file is a stub. The descriptions below are taken from the skill inventory; the full failure entries need to be written here.

The four that hurt most (from the skill description):

1. Stripping the whole META-INF/ during a repack deletes ServiceLoader registrations and the app dies at startup with an error that names an unrelated library.
2. Patching a byte-level string without preserving string_ids ordering gets the whole dex rejected, while checksums and signatures verify perfectly.
3. Rebuilding a dex with a whole-tree smali round-trip damages R8 output invisibly — class tables compare clean, and it only blows up at runtime.
4. Neutralising a native terminate path by making it not return. A spinning stub does not suppress the check; it freezes the caller and every thread behind it. The app hangs with no crash record at all, and the eventual death gets blamed on whatever killed the frozen process.
