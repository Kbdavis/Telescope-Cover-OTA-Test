# Telescope Cover OTA test pipeline

Public artifacts for an isolated spare-board OTA experiment. These are bench builds, not operational telescope-cover firmware. No servo, UART1 bus, telescope-control or cover-motion worker is linked.

- **beta.1 / bench-1.bin:** initial USB bench application, installed only over the pinned existing 4 MB partition layout. Requires Wi-Fi already configured by the controller firmware.
- **beta.2 / bench-2.bin:** signed OTA application for the exact beta.1 ELF listed in artifacts.json.
- **index.json:** browser-readable bench catalog, intersected with published prereleases on this repository.
- **manifest-2.txt / .sig:** exact canonical manifest bytes and P-256/SHA-256 P1363 signature (hex). Public verification key is included.

The controller generates its own update authorization key in NVS and prints it only to its USB console. No Wi-Fi password, device update key, private signing key, private project checkout or device backup is published here.

The webpage downloads the image from GitHub Pages, then sends bounded chunks to the spare, which verifies the signed source transition and whole-image hash before activation. It confirms the new ELF, transaction, accepted startup and preserved application-settings record after reboot.

Keep USB recovery available. These files are not a first-install factory image and must not be used to protect a telescope. Bench results do not qualify production OTA or servo safety.
