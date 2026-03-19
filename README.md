# USB Mount Blocker

Interactive Bash tool for blocking automatic mounting and media import handling for selected USB devices.

## Why This Exists

This project was created mainly for a phone connected to the PC for charging.

The goal is:

- keep the phone charging
- stop Linux desktop auto-mount and media-import behavior
- still allow manual access when wanted

It also works for normal USB storage devices such as flash drives.

## What It Does

- lists connected USB storage and phone/media devices
- lets the user block one or more devices
- stores the blocklist in a local config file
- generates `udev` rules to stop automount and related media handling
- lets the user view blocked devices
- lets the user unblock devices

For USB storage devices, the script uses `UDISKS_AUTO=0`, so manual mounting is still possible.

For phones and media-style devices such as an iPhone, the script also generates extra `udev` override files so the same hardware is not treated as:

- a browseable phone device
- an MTP device
- a camera/PTP import device

## Files

- `usb_mount_blocker.sh`: main tool
- `~/.config/usb-mount-blocker/blocklist.tsv`: saved blocklist

Generated rule files:

- `/etc/udev/rules.d/61-usb-mount-blocker.rules`
- `/etc/udev/rules.d/59-usb-mount-blocker-media.rules`
- `/etc/udev/rules.d/39-usbmuxd.rules`
- `/etc/udev/rules.d/60-libgphoto2-6t64.rules`
- `/etc/udev/rules.d/69-libmtp.rules`
- `/etc/udev/rules.d/70-uaccess.rules`
- `/etc/udev/rules.d/90-libgpod.rules`
- `/etc/udev/rules.d/95-cd-devices.rules`

## Requirements

- `bash`
- `lsblk`
- `udevadm`
- `sudo` or root access for writing rule files under `/etc/udev/rules.d/`

## Usage

Interactive mode:

```bash
./usb_mount_blocker.sh
```

Direct commands:

```bash
./usb_mount_blocker.sh list
./usb_mount_blocker.sh block
./usb_mount_blocker.sh blocked
./usb_mount_blocker.sh unblock
./usb_mount_blocker.sh rebuild
```

## Matching Strategy

USB storage devices are matched in this order:

1. `ID_SERIAL_SHORT`
2. `ID_SERIAL`
3. filesystem UUID
4. vendor ID + model ID fallback

Phone/media devices are matched by:

1. USB vendor ID
2. USB product ID
3. USB serial, when available

For iPhones and similar devices, one blocked phone entry also covers the related camera/PTP behavior from the same physical USB device.

## Notes

- This script does not block a USB port. It blocks specific devices.
- If a flash drive behaves differently on one physical port, that is not caused by a port-based rule in this project.
- Manual mounting should still be possible for blocked USB storage devices.
