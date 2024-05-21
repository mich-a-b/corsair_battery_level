# Corsair Battery Level
Displays the current state and battery level of a Corsair headset in the system tray of Ubuntu.

It should work with most corsair headsets.

## Usage 
You can clone the repo, run `npm i` then `node app.js`

## Build instructions
Run `npm i` then run the build script `npm run build`

## Dependencies:
* [node-hid](https://github.com/node-hid/node-hid)
* [systray2](https://github.com/felixhao28/node-systray)

##

From what it seems, the original repo, [SaifAqqad/corsair_battery_level](https://github.com/SaifAqqad/corsair_battery_level), is only intended for use on Windows.

Nodejs being cross platform, it isn't much of a stretch to make it compatible for Ubuntu.

## Notes And Errors

### The first error I got, was the following:

```bash
node:internal/errors:478
    ErrorCaptureStackTrace(err);
    ^

Error: spawn /home/user/.cache/node-systray/2.1.4/tray_linux_release EACCES
    at Process.ChildProcess._handle.onexit (node:internal/child_process:285:19)
    at onErrorNT (node:internal/child_process:485:16)
    at processTicksAndRejections (node:internal/process/task_queues:83:21) {
  errno: -13,
  code: 'EACCES',
  syscall: 'spawn /home/user/.cache/node-systray/2.1.4/tray_linux_release',
  path: '/home/user/.cache/node-systray/2.1.4/tray_linux_release',
  spawnargs: []
}

```

This can be fixed with the following command:

```bash
chmod +x ~/.cache/node-systray/2.1.4/tray_linux_release
```

### The next challenge I had, was the following error:

```bash
Failed to initialize HID device: TypeError: cannot open device with path /dev/hidraw3
    at new HID (/home/user/source/apps/corsair_battery_level/node_modules/node-hid/nodehid.js:57:17)
    at get_HID (/home/user/source/apps/corsair_battery_level/app.js:113:25)
    at init_device (/home/user/source/apps/corsair_battery_level/app.js:82:33)
    at processTicksAndRejections (node:internal/process/task_queues:96:5)

```

The fix for this was also permission based, and would depend on your device name. For me it was `/dev/hidraw3`.
There is a temporary fix, but to make sure the fix persist with reboot, please create the following `udev` entry.

```bash
sudo nano /etc/udev/rules.d/corsair-headset.rules
```

Inside of the file, add the following, and save:

```bash
SUBSYSTEM=="hidraw", ATTRS{idVendor}=="1b1c", ATTRS{idProduct}=="0a4f", MODE="0666"
```

- replace `0a4f` with the actual id for your headset. it can be found by running the following:

    `sudo apt-get install usbutils`

    `lsusb`

After saving the file, run the following two commands to make sure it takes effect:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

After applying these fixes, running `node app.js` should display your headset's battery in the system tray.