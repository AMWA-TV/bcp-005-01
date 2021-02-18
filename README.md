# AMWA NMOS EDID Connection Management Specification

[![Lint Status](https://github.com/AMWA-TV/nmos-edid-connection-management/workflows/Lint/badge.svg)](https://github.com/AMWA-TV/nmos-edid-connection-management/actions?query=workflow%3ALint)
[![Render Status](https://github.com/AMWA-TV/nmos-edid-connection-management/workflows/Render/badge.svg)](https://github.com/AMWA-TV/nmos-edid-connection-management/actions?query=workflow%3ARender)

<!-- INTRO-START -->

## What does it do?

- Provides an architecture that enables PRO AV User Stories, previously handled by mediums like HDMI or DisplayPort, in an NMOS environment.

### Core User Stories

1. As a user with a computer that is connected to an HDMI-IPMX gateway (sender) that is connected through a network to an IPMX-HDMI gateway (receiver) plugged into an HDMI monitor with EDID, I would like to be able to plug my computer in and have the best video resolution displayed.

2. As a user with a computer that is connected as above, I'd like to change the resolution of my display to support graphics mode that is lower resolution, so that my graphic intensive program plays back more smoothly.

3. As a pro AV installer I have a set of gateway devices that I send out to my staff, when they set up remote presentations to support our hotel guests. I have the gateway devices configured to auto-connect to each other, as soon as they turn on. The specific monitor or projector (sink) and source are unknown, until they are plugged into the gateway devices. I want my staff to be able to plug a network cable directly into each device (sender and receiver). I have configured these devices to automatically connect to each other when they are directly connected via a network cable... Basically, I want my gateway to work like an HDMI/DisplayPort cable would.

4. As a Pro AV installer, I've added new monitors to the digital signage system that uses IPMX to multicast transport video to my displays. When I plug them in, they negotiate to 4K60, but the rest of the monitors are 1080p. I need the system to play my digital signage content on all of my system's monitors at the best profile supported by all monitors.

5. As a Pro AV digital signage installer, I need to colour-match the monitors on my video wall. To do this, I need to read the detailed EDID information, which includes the monitor's colour profile, model number and other such information, which I use in my software for calibration.

6. As a Pro AV installer, I have developed some in house EDID software that reads the more detailed data from the monitor, such serial numbers, manufacture date and colour management information. When I make video walls for customers, I use IPMX AV over IP hardware to deliver content. I would like to be able to retrieve the EDID binary representation from my monitors into my home-built software, so that I can use that information to adjust the monitors and manage my system.

## Why does it matter?

- PRO AV Users of NMOS require support for user stories tailored to the PRO AV world
- To transition to IP and leverage NMOS, existing EDID workflows need to be adapted to match the new technologies

## How does it work?

- Builds on existing NMOS Specifications including extended Receiver Capabilities
- Adds a new IPMX Device Controller and associated API

<!-- INTRO-END -->

## Getting started

There is more information about the NMOS Specifications and their GitHub repos at <https://specs.amwa.tv/nmos>.
