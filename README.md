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

## Configuration UI and global update badge (beta.5)

Beta.5 is a signed OTA upgrade from the exact beta.4 ELF. It opens on Overview, integrates updating into Configuration → Firmware updates, and adds the compact blue Update available header badge across Overview, Calibration, Configuration and Manual Control. Clicking the badge opens/focuses Firmware updates. Automatic checking defaults on and its checkbox preference is remembered in the browser. Manual checks still work when automatic checking is off. No update key is entered.

The UI was tested in an isolated real-browser fixture at 1440, 1024 and 390 pixels; its future-release notification used synthetic beta.6 metadata only for rendering tests. No beta.6 artifact was published. Beta.5 is left for the owner to install with the current beta.4 page; it has not been flashed by automation. This remains the isolated update-test build: calibration, servo/telescope motion and operational configuration editing remain unavailable.

The real installed beta.4 page has been checked read-only and advertises beta.5 from the public release/catalog. No controller update or reboot was performed. [Validation details](validation-beta5.json) distinguish UI fixtures from owner-pending hardware installation.

## Complete controller candidates

The complete controller is being qualified separately from the isolated bench firmware. Owner-requested beta.10 is a public **download/install/reboot test prerelease**, with no functional changes from beta.9. Its signed `controller-index.json` entry targets only the exact full-controller beta.9 ELF. Use Configuration → Firmware updates → Check now on that controller. It is not a qualified production release.

The installed receiver still requires a powered, verified native servo and honest stationary CLOSED/calibration evidence. An unpowered spare will reject installation at admission; no safety checks have been bypassed. Powered full-workload update/reboot/rollback and cold-start qualification remain pending. The production controller has not been modified.

`controller-10-source.tar.gz` contains the exact curated firmware source; `controller-10-notices.zip` retains project and dependency notices. `controller-10-release.json` records the source, image hashes and test-only scope. The original bench catalog and binaries retain their existing authority and identities.

## Warning-confirmed updates (beta.11 → beta.12)

The owner requested a warning checkbox instead of powered-servo/CLOSED/calibration admission for browser updates. Beta.11 is the USB bootstrap on the spare; beta.12 is its signed public follow-up for end-to-end testing. After confirming that the mechanism is physically secured and the telescope is safe, updates may proceed with the servo unpowered, disconnected or unverified. Active motion/hold, maintenance conflicts, network loss, signature/hash failure and insufficient memory still block or stop an update. Firmware updates never establish motion readiness or rewrite servo configuration. An unavailable servo keeps motion disabled after restart.

The Firmware updates panel now displays actionable controller reasons directly, and a missing acknowledgement is rejected with an instruction to check the warning. The earlier beta.9/10 powered-servo restriction is superseded in this new receiver. Production hardware remains unchanged.
