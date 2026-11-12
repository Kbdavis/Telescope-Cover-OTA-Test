# Telescope Cover OTA test pipeline

Public artifacts for an isolated spare-board OTA experiment. These are bench builds, not operational telescope-cover firmware. No servo, UART1 bus, telescope-control or cover-motion worker is linked.

- **beta.1 / bench-1.bin:** initial USB bench application, installed only over the pinned existing 4 MB partition layout. Requires Wi-Fi already configured by the controller firmware.
- **beta.2 / bench-2.bin:** signed OTA application for the exact beta.1 ELF listed in artifacts.json.
- **index.json:** browser-readable bench catalog, intersected with published prereleases on this repository.
- **manifest-2.txt / .sig:** exact canonical manifest bytes and P-256/SHA-256 P1363 signature (hex). Public verification key is included.

From beta.3 onward, the webpage obtains a fresh boot-scoped authorization token automatically from the controller on the trusted home LAN. No key entry, browser pairing or USB lookup is required. Anyone who can open that controller page can explicitly install a compatible signed bench update. Legacy beta.1/beta.2 used a USB-console key; their NVS key is retained for fallback compatibility. No Wi-Fi password, runtime authorization token, private signing key, private project checkout or device backup is published here.

The webpage downloads the image from GitHub Pages, then sends bounded chunks to the spare, which verifies the signed source transition and whole-image hash before activation. It confirms the new ELF, transaction, accepted startup and preserved application-settings record after reboot.

Keep USB recovery available. These files are not a first-install factory image and must not be used to protect a telescope. Bench results do not qualify production OTA or servo safety.

## Measured happy-path result

**PASS — 2026-11-12.** The actual spare webpage discovered beta.2 through the public GitHub release list and Pages catalog, downloaded the signed image, transferred 839,440 bytes to the inactive slot, rebooted from beta.1/app0 into beta.2/app1, and confirmed the exact new ELF, matching durable transaction, accepted startup and unchanged application-settings record. Download/install/reboot confirmation took 254.291 seconds.

Wrong device-key and invalid-signature requests were rejected before flash admission. See [sanitized acceptance data](acceptance.json), [update available](update-available.png), and [successful completion](update-complete.png).

This result covers the isolated bench happy path. Power-loss/rollback fault injection, production motion admission, operational UI integration and unattended deployment remain separate qualification work.

## Automatic authorization (beta.3+)

Beta.3 introduces the automatic session flow. Beta.4 provides the next compatible release for testing that flow without entering any key. Signed-image verification and explicit install confirmation remain. Real acceptance passed: legacy beta.2 migrated to beta.3, then beta.3 → beta.4 completed from the webpage with no key-file argument, manual key input or pairing. Reboot, exact target, matching transaction and unchanged settings were confirmed. Invalid/missing authorization, foreign session origin, missing install confirmation, invalid signature and stale post-reboot token were rejected.

The first keyless attempt lost its LAN connection and aborted at 8,064 bytes before activation; after one deliberate recovery restart, the fresh retry completed in 257.186 seconds. The connection-loss cause remains unestablished. See [full sanitized result](acceptance-auto-auth.json), [keyless completion](automatic-update-complete.png), and [fresh page without a key field](automatic-update-page.png).
