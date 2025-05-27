# RetroComputersWithAI
A guide to Bring the AI wisdom to retro computers. (plus other stuff like web-text browsing)

## Introduction

AI tools provides the users to have super powers in terms of wisdom.
For example, if the user does not know which are the commands to perform a partitioning to a hard disk, the AI can assist the user, provide the correct commands, plus giving explanation about all the involved steps.

The idea is to provide retro-computers (e.g. MS-DOS, windows 98 PCs) access to a modern AI tools and some basic text web browsing experience

This also allows the retro computer to navigate on the internet in basic text mode, and to visit bulletin board system (BBS).

## Screenshots from my win98 laptop

![AI running in the terminal](/screenshots/w98AIdemo.jpg "AI running on hyper-terminal")
![Wikipedia running in the terminal](/screenshots/w98wiki.jpg "Wikipedia running in the terminal")
![information of the SBC](/screenshots/w98neofetch.jpg "information of the SBC")
![links terminal web browser](/screenshots/w98asciicats.jpg "links terminal web browser")

## Main concept

It is a fact that retrocomputers have an infinite amount of impediments to get connected to the modern digital world.

However, a single board computer (**SBC**), even the most simple one with 1GB of RAM, is able to run Linux and do some basic interactions with the modern internet at console level.

The idea is to use a port that can be found in almost every retro computer
as follows:

```mermaid
graph TD
    A[Old PC running Windows 98/DOS] -->|Serial port via RS232| B(Null Modem Cable)
    B -->|RS232| C(USB to RS232 Adapter)
    C -->|USB| D[Orange Pi Zero 2 Running Linux]
    D -->|Internet| E(AI Tool - TGPT)
```
## Ingredients

- Any modern computer, or any SBC
- Linux (FreeBSD may also work, but linux is recommended)
- tgpt (GPT that runs diirectly from any terminal) https://github.com/adamyodinsky/TerminalGPT
- USB to RS232 adapter. (Optionally you can directly interface the SBC RS232 interface to the com port, but be careful and make sure the voltage is compatible on both devices)
- null modulation cable ( *very important!* )
- agetty (99.99% of the time is included with any linux distro)

## SBC configuration

The single board computer has to be configured first. As this device will interact with the internet.

### 1. Installing an operative system on the SBC

In my case, i used debian. I used the one included in the orange pi zero webpage. If possible I recomend that you use the distro that comes with your SBC computer, but no matter the distro, some of the steps may be very similar.

So go and install the distro.

In my case, i had to perform the following:

1. Download the ***.img** file from the website
2. Download and use a tool called Balena etcher (https://etcher.balena.io/)
3. write the system directly to a microSD card
4. Insert the card on the SBC (my orange pi)
5. The system was running.

**Important note:** Pay much attention to the current user names and passwords.
In my case, the user was already preconfigured in the image. The user name was **orangepi** and the password was **orangepi**. This user has admin permissions, so nothing had to be configured.

Screenshot of the orangepi running:

![OrangePi running](/screenshots/OrangepiBoot.jpg "OrangePi running")

### 2. Add the current user to the dialout group

An important part to be able to user serial communications, is to add the current user to the dialout group. 

```Bash
sudo usermod -a -G dialout "$USER"
```

The **$USER** variable automatically expands to your current username, so you don't have to type it out. Quoting "$USER" is good practice to prevent issues if your username ever contains spaces (though this is rare in Linux usernames).

to verify the changes were applied corrrectly, run the command

```bash
groups "$USER"
```

You should see dialout listed among your groups.

Screenshot of the dialout configuration:
![Dialout configuration](/screenshots/dialoutuser.jpg "Dialout configuration")

### 3. (optional) Installing minicom to test the serial communication

You can test both devices, up to this point no gpt magic functionality is present. but it is good to test if both devices are ready, or if the hardware already works. This is highly recommended.

This step is performed on the SBC, but you can also do it in another computer just to test the cables.

1. install minicom (as i am in debian i have to run)
```bash
sudo apt-get install minicom
```
2. run minicom
```bash
minicom -s
```
if everything is correct, then you shall see the following menu:

![minicom settings menu](/screenshots/minicom settings.jpg "minicom settings menu")

select serial port setup and the following screen shall appear:

![minicom port settings](/screenshots/minicomportsettings.jpg "minicom port settings")

when you finish the configurations, hit enter, then you will be redirected to the previous menu.
Select **Exit**  (Exit from the menu, not exit from minicom)

Then you should see the following:

![minicom screen](/screenshots/minicomscreen.jpg "minicom screen")

In the other side (the other computer or device) you shall see a basic connection, at least what you write in one side, shall be seen on the other side.

### 4. Install tgpt on the SBC

This step is pretty simple, it is fully documented on the developer website, but it is almost like magic, what i only did is to run this on the terminal

```bash
curl -sSL https://raw.githubusercontent.com/aandrew-me/tgpt/main/install | bash -s /usr/local/bin
```

The screenshots is from another computer, but it is the same on the SBC

![tgptinstallation](/screenshots/tgptinstall.jpg "tgpt installation")

the usage is very simple, and I recommend you to use **tgpt** in combination with the linux command **less**.
This allows the user to scroll text when responses are long.

```bash
tgpt "your question here" | less
```
Note: I recommend you to write the quotation marks.

Example:
![aiquestion](/screenshots/aiquestion.jpg "Question for the AI")

![airesponse](/screenshots/airesponse.jpg "Response from the AI")

You can scroll with the keyboard using the arrow keys.
**Press Q to quit scrolling** and return to the terminal.

### 5. Configure agetty (under construction 🚧)

Linux distributions allow to perform remote connections directly to a tty interface.
For simplicity **SSH is not going to be used**. The connection to the single board computer will be then a simple serial connection.




## Retro computer tips

I used the speed 115200, but some other devices only support 9600 bauds. Specially under MSDOS.
Consider thisa when setting the agetty parameters.
