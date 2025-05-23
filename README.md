# RetroComputersWithAI
Giving wisdom to retro computers

## Introduction

AI tools enable the user to have super powerrs in terms of wisdom.
For example, if the user does not know which are the commands to perform a partitioning to a hard disk, the AI can assist the user, provide the correct commands, plus giving explanation about all the involved steps.

The idea is to somehow provide retrocomputers (e.g. MS-DOS, windows 98) access to a modern AI tools

## Constraints

Technological limitations make virtually impossible to just plug an ethernet cable and open AI tools from the browser, because of dozens of reasons. From low level protocol, to CPU resources, etc.

## Posibilities

:) Fortunely, there are a set of tools that can help to achieve ths mission.

Old computers have:
RS232, or USB, software like hyperterminal.

single board computers has, USB, ethernet/wifi, modern linux. internet connectivity etc.
No GUI is needed on the SBC, as there are now software tools that allow the user to interact with AI in text-only

## Main concept

Old PC > RS232 > USB to RS232 > Orange Pi Zero 2 (1GB ram) > Internet > AI tool.

## Challenges

### The SBC computer

#### HW Specs

CPU Allwinner H616 64-bit high-performance Quad-core Cortex-A53 processor
GPU Mali G31 MP2
Memory(SDRAM)	512MB/1GB DDR3 (Shared with GPU）

#### Operative system

Downloaded and booted the official Debian image found at oragepi webpage

#### Setting the connection

Accessing your Orange Pi Zero 2 via RS232 (serial console) is a classic and robust way to interact with it, especially for debugging or when network access isn't available. Your plan with the two USB to RS232 adapters and a null modem cable is sound for establishing a direct DTE-to-DTE connection.

Here's a detailed breakdown of how to achieve this, covering both the hardware and software aspects:

## Hardware Setup

Your proposed hardware setup is correct:

* **Linux Laptop:** This will be your terminal.
* **USB to RS232 Adapter (Laptop side):** This converts your laptop's USB to a male DB9 RS232 port.
* **Null Modem Cable:** This is crucial. It cross-connects the transmit (TX) and receive (RX) lines (and sometimes handshake lines) between the two RS232 devices. You cannot use a straight-through serial cable.
* **Second USB to RS232 Adapter (Orange Pi side):** This converts the Orange Pi's USB to a male DB9 RS232 port. You'll need to ensure this adapter is recognized by the Orange Pi's Debian installation.
* **Orange Pi Zero 2:** This is your target device.

**Important Note on the Orange Pi side:** The Orange Pi Zero 2 has dedicated UART pins on its expansion headers. Using a USB to RS232 adapter on the Orange Pi's USB port will work, but it's often more common to use a USB to **TTL** serial adapter connected directly to the Orange Pi's UART debug pins (often UART0 or UART5, depending on the specific pinout and image configuration). This eliminates the need for the second USB to RS232 adapter and the null modem cable, as the USB to TTL adapter handles the USB-to-serial conversion and level shifting.

**If you stick to your current plan (USB to RS232 on both sides):**

1.  **Connect everything:**
    * Laptop USB port -> USB to RS232 Adapter (Laptop)
    * USB to RS232 Adapter (Laptop) -> Null Modem Cable -> USB to RS232 Adapter (Orange Pi)
    * USB to RS232 Adapter (Orange Pi) -> Orange Pi Zero 2 USB port

## Software Configuration (Orange Pi Zero 2 - Debian)

You need to ensure that Debian on your Orange Pi is configured to output a login prompt on the serial port that corresponds to the USB to RS232 adapter you're using.

1.  **Identify the Serial Port Device:**
    * Boot your Orange Pi and access it via microHDMI and keyboard/mouse for initial setup.
    * Plug in the USB to RS232 adapter into one of the Orange Pi's USB ports.
    * Open a terminal on the Orange Pi.
    * Run `lsusb` to see if the USB serial adapter is detected. You should see an entry for it (e.g., "Prolific Technology, Inc. PL2303 Serial Port" or "FTDI FT232R USB UART").
    * Run `dmesg | grep tty` to see what serial device name the kernel assigned to it. It will likely be something like `/dev/ttyUSB0`. This is the device you'll configure for the serial console.

2.  **Enable agetty for the Serial Port:**
    Debian (using systemd) uses `agetty` to manage login prompts on serial ports.

    * **Create a systemd service override:** This is the recommended way to enable a `getty` instance on a specific serial port without modifying the original service files. Replace `/dev/ttyUSB0` with the actual device name you found in the previous step.

        ```bash
        sudo systemctl enable serial-getty@ttyUSB0.service
        sudo systemctl start serial-getty@ttyUSB0.service
        ```

        This will create a symlink to enable the service on boot and start it immediately.

    * **Verify the service:**
        ```bash
        sudo systemctl status serial-getty@ttyUSB0.service
        ```
        It should show as "active (running)".

    * **Optional: Configure Baud Rate (usually 115200 for debug consoles):**
        While `agetty` can auto-detect, it's good practice to ensure it's set. The default `serial-getty@.service` typically uses 115200 baud. If you need a different baud rate or other settings, you might need to create an override file:

        ```bash
        sudo systemctl edit serial-getty@ttyUSB0.service
        ```
        Add the following content (replace `ttyUSB0` and `115200` if needed):

        ```
        [Service]
        ExecStart=
        ExecStart=-/sbin/agetty -o '-p -- \\u' --keep-baud --autologin %I 115200,$TERM
        StandardInput=tty
        StandardOutput=tty
        ```
        Then save and exit. Reload systemd and restart the service:
        ```bash
        sudo systemctl daemon-reload
        sudo systemctl restart serial-getty@ttyUSB0.service
        ```
        **Note:** The `ExecStart=` line resets the command, and the second `ExecStart=` then defines it. `autologin %I` will log in as the user specified by `%I` (which will be `ttyUSB0` in this case, so you'd actually want to specify a user or remove `autologin` for a login prompt). For a direct login, you could use `--autologin orangepi` instead of `%I`. For a standard login prompt (asking for username and password), omit `--autologin`.

3.  **Kernel Console (for boot messages - advanced but useful):**
    If you want to see kernel boot messages on your serial console, you'll need to modify the kernel command line. This is typically done by editing `/boot/armbianEnv.txt` (if you're using Armbian, which is common for Orange Pis) or `cmdline.txt` on a standard Debian image.

    Look for a line starting with `console=`.
    * If you see `console=ttyS0` or similar, that's likely the built-in UART.
    * You might need to add `console=ttyUSB0,115200` to this line, or modify an existing one.
    * **Be extremely careful when modifying kernel command lines**, as a mistake can prevent the system from booting. Make a backup of the file first.
    * Example: `console=ttyS0,115200 console=ttyUSB0,115200` (this would send output to both).

    After modifying the kernel command line, you must reboot the Orange Pi for the changes to take effect.

## Software Configuration (Linux Laptop)

On your Linux laptop, you'll use a serial terminal program to connect to the USB to RS232 adapter.

1.  **Identify the USB to RS232 Adapter Device:**
    * Plug your USB to RS232 adapter into your Linux laptop.
    * Run `lsusb` to confirm it's detected.
    * Run `dmesg | grep tty` or `ls /dev/ttyUSB*` to find its device name (e.g., `/dev/ttyUSB0`).

2.  **Install a Serial Terminal Program:**
    Popular choices include `minicom`, `screen`, or `picocom`.

    * **minicom:**
        ```bash
        sudo apt update
        sudo apt install minicom
        ```
        To run: `minicom -D /dev/ttyUSB0 -b 115200` (replace `/dev/ttyUSB0` with your device and `115200` with the baud rate configured on the Orange Pi).

    * **screen:**
        ```bash
        sudo apt update
        sudo apt install screen
        ```
        To run: `screen /dev/ttyUSB0 115200` (replace `/dev/ttyUSB0` and `115200`).
        *To exit screen:* Press `Ctrl+A`, then `K`, then confirm with `y`.

    * **picocom:**
        ```bash
        sudo apt update
        sudo apt install picocom
        ```
        To run: `picocom -b 115200 /dev/ttyUSB0` (replace `/dev/ttyUSB0` and `115200`).
        *To exit picocom:* Press `Ctrl+A`, then `Ctrl+X`.

3.  **Set Permissions:**
    You might need to add your user to the `dialout` group to access serial ports without `sudo`:
    ```bash
    sudo usermod -a -G dialout your_username
    ```
    You'll need to log out and log back in for this change to take effect.

## Troubleshooting

* **No output:**
    * Double-check your cable connections, especially the null modem cable. Ensure it's not a straight-through cable.
    * Verify the serial device names on both ends (`/dev/ttyUSBX`).
    * Confirm the baud rate setting is identical on both the Orange Pi (`agetty` configuration) and your laptop's serial terminal program.
    * Check the `agetty` service status on the Orange Pi.
    * If using the kernel console, ensure the `console=` parameter is correctly set in the boot configuration file and the Orange Pi has been rebooted.
* **Garbled text:**
    * Mismatched baud rate is the most common cause.
    * Incorrect parity or stop bit settings (though `agetty` defaults are usually 8N1, which is standard).
* **Permission denied:**
    * Ensure your user is in the `dialout` group on your Linux laptop, or use `sudo` to run the terminal program.
* **The Orange Pi doesn't recognize the USB to RS232 adapter:**
    * Ensure the adapter is a standard one with common chipsets (like FTDI, Prolific, Silicon Labs CP210x), as these usually have drivers built into the Linux kernel.
    * Check `dmesg` output on the Orange Pi after plugging in the adapter to see if there are any error messages.

This detailed guide should help you get your RS232 connection to your Orange Pi Zero 2 up and running!



### RS232 adapters

I have 2 USB to Rs232 cables, and a null modulation cable.
I've performed a test in linux, and everything worked fine.

Notes: In linux, remember the following

 - To add the cuurrent user to the dial up group.
 - You can use picocom to perform a test by executing
   picocom -b 9600 /dev/ttyUSB1


