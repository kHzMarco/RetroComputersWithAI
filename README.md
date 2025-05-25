# RetroComputersWithAI
A guide to Bring the AI wisdom to retro computers

## Introduction

AI tools provides the users to have super powers in terms of wisdom.
For example, if the user does not know which are the commands to perform a partitioning to a hard disk, the AI can assist the user, provide the correct commands, plus giving explanation about all the involved steps.

The idea is to provide retrocomputers (e.g. MS-DOS, windows 98 PCs) access to a modern AI tools and some basic text web browsing experience

## Screenshots

![AI running in the terminal](/screenshots/w98AIdemo.jpg "AI running on hyperterminal")
![Wikipedia running in the terminal](/screenshots/w98wiki.jpg "Wikipedia running in the terminal")
![information of the SBC](/screenshots/w98neofetch.jpg "information of the SBC")
![links terminal web browser](/screenshots/w98asciicats.jpg "links terminal web browser")

## Main concept

It is a fact that retrocomputers have an infinite ammount of impediments to get connected to the modern digital world.

However, a single board computer, even the most simple ones with 1GB of RAM, is able to run Linux and do some basic interactions with the modern internet at console level.

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
- tgpt https://github.com/adamyodinsky/TerminalGPT
- USB to RS232 adapter (optionally you can directly interface the SBC RS232 interface to the com port, but... I prefer the USB to RS232 adapter)
- null modulation cable *really important*
- agetty (99.99% of the time is included with any linux distro)
