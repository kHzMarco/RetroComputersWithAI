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

CPU 	Allwinner H616 64-bit high-performance Quad-core Cortex-A53 processor
GPU 	• Mali G31 MP2
Memory(SDRAM) 	512MB/1GB DDR3 (Shared with GPU）

#### Operative system

According to the specs. ARMbian could be an operative system.
