# a50-headset-manager

Automatic audio input/output switching daemon for the Astro A50 wireless headset
(generation 4).

This daemon monitors your A50 headset status and automatically switches your
system's default audio output (speakers/headphones) and input (microphone):

- **Headset active** (off dock, powered on): Routes audio output to the A50 headset
  and sets the A50 microphone as the default input
- **Headset docked/disconnected**: Falls back to HDMI output (if monitor connected)
  or internal speakers, and switches the microphone to the internal mic array

## Tested Configuration

This daemon has only been tested on:
- **Hardware:** Framework 16 laptop
- **OS:** Arch Linux
- **Audio:** PipeWire with WirePlumber

It may work on other Linux configurations but is not guaranteed.

## Requirements

- Linux with PipeWire/PulseAudio
- Python 3.10+
- Astro A50 Gen 4 headset and base station

## Installation

### Using pipx (recommended)

```bash
pipx install git+https://github.com/gmg-catapultam/a50-headset-manager.git
```

### From source

```bash
git clone https://github.com/gmg-catapultam/a50-headset-manager.git
cd a50-headset-manager
pipx install .
```

## USB Access (required)

Create a udev rule to allow non-root access to the A50 base station:

```bash
echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="9886", ATTR{idProduct}=="002c", MODE:="0666"' | \
    sudo tee /etc/udev/rules.d/50-astro-a50.rules
```

Re-plug your base station to apply the rule.

## Running as a systemd service

Copy the service file to your systemd user directory:

```bash
mkdir -p ~/.config/systemd/user
cp a50-headset-manager.service ~/.config/systemd/user/
```

Or if installed via pipx/pip, download the service file:

```bash
mkdir -p ~/.config/systemd/user
curl -o ~/.config/systemd/user/a50-headset-manager.service \
    https://raw.githubusercontent.com/gmg-catapultam/a50-headset-manager/main/a50-headset-manager.service
```

Enable and start the service:

```bash
systemctl --user daemon-reload
systemctl --user enable a50-headset-manager
systemctl --user start a50-headset-manager
```

Check status:

```bash
systemctl --user status a50-headset-manager
```

View logs:

```bash
journalctl --user -u a50-headset-manager -f
```

## Running manually

```bash
a50-headset-manager
```

## Audio Priority

### Output (speakers/headphones)
1. A50 headset (when active)
2. HDMI output (when monitor with audio is connected)
3. Internal speakers

### Input (microphone)
1. A50 headset microphone (when active)
2. Internal microphone array
3. External microphone input

## Acknowledgments

Uses [eh-fifty](https://github.com/tdryer/eh-fifty) by Tom Dryer, a Python
library for configuring the Astro A50 headset (MIT licensed).

## License

MIT License - see [LICENSE](LICENSE) for details.
