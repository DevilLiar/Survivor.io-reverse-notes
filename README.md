# Survivor.io-reverse-notes
Reverse engineering findings on Survivori.io, by Hxd
# Survivor.io Reverse Engineering Notes (by HxD)

This is a public log of reverse engineering attempts and discoveries made by **HxD** while analyzing the mobile game **Survivor.io**, which uses Unity IL2CPP and advanced anti-tamper protections.

## Findings So Far

- `libil2cpp.so` is not loaded during app startup. It loads **dynamically**, likely near the end of gameplay or after a match concludes.
- Hooking `libil2cpp.so` using Frida on `dlopen()` works—but results in a crash shortly after, implying:
  - Anti-hooking logic
  - **Watchdog** behavior (memory or load validation thread)
- SSL pinning is active and likely handled in both Java and native code. Standard Frida bypasses may not be sufficient.
- The crash behavior suggests **integrity or behavior-based detection**, not just static memory checks.
- Traditional asset mining tools (AssetStudio, APK unzip) can extract images but not code logic.

## Theories

- A background thread may monitor `libil2cpp.so` memory or validate function pointer integrity
- The game may preload watchdog logic in another `.so` before actual gameplay starts
- Hooking too early (or touching critical memory areas) triggers a silent tamper flag

## Tools Used

- Frida
- AssetStudio
- ADB + emulator (rooted)
- Manual `dlopen()` tracing
- Memory exploration

## Next Steps (For Others)

- Hook `dlopen()` with a delay + passive logging (no patching yet)
- Look for native `SSL_*` or `BIO_*` calls for traffic sniffing
- Dump runtime memory map after `libil2cpp.so` is loaded and idle
- Confirm watchdog behavior by inspecting memory post-match
- Consider using Frida Gadget with obfuscated `.so` names

---

This research is ongoing and experimental. If you're building on this, credit **HxD**.
