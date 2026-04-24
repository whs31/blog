+++
title = "GCS"
description = "Cross-platform ground control station for UAV flight planning, telemetry, and mission execution."
date = 2024-03-10
template = "project.html"

[extra]
skills = ["UAV", "Qt / C++", "Networking", "Embedded"]
slides = [
  {src = "/img/gcs-1.jpg", type = "image"},
  {src = "/img/gcs-2.jpg", type = "image"},
  {src = "/img/gcs-3.jpg", type = "image"},
]
+++

## Overview

GCS (Ground Control Station) is a desktop application for planning, monitoring, and commanding unmanned aerial vehicles. It communicates with autopilot firmware over MAVLink, rendering live telemetry, a moving-map display, and a mission editor in a single unified interface.

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Curabitur vitae sem quam. Suspendisse potenti. Nullam vitae mauris accumsan, dignissim nisl non, lobortis ligula.

## Features

### Flight Planning

The mission editor lets operators define waypoints, survey grids, and return-to-home sequences on an interactive map. Nullam vulputate risus eget feugiat ornare. Cras nec erat quis ligula tristique faucibus vel sit amet nisi. Plans are validated for geofence compliance and estimated flight time before upload.

### Live Telemetry

A customizable instrument panel displays altitude, airspeed, heading, battery state, and GNSS fix quality in real time. Donec sed odio dui. Donec ullamcorper nulla non metus auctor fringilla. Vestibulum id ligula porta felis euismod semper. All incoming MAVLink frames are logged to disk for post-flight analysis.

### Video & Payload Control

An integrated video widget decodes H.264 streams from the onboard camera over UDP and presents them alongside the telemetry view. Cras mattis consectetur purus sit amet fermentum. Cras justo odio, daccumulan eget facilisis at. Gimbal pitch and roll commands are forwarded through the same MAVLink link.

### Multi-Vehicle Support

GCS handles fleets of up to eight vehicles simultaneously using a tabbed vehicle panel. Each vehicle maintains its own MAVLink system ID, parameter set, and telemetry history. Sed posuere consectetur est at lobortis. Aenean lacinia bibendum nulla sed consectetur.

## Technical Stack

Built with **Qt 6 / C++20** for a native cross-platform UI (Linux, Windows, macOS). The MAVLink parsing layer is generated from XML message definitions and runs on a dedicated background thread to keep the UI responsive. Map tiles are served locally via an embedded tile server to support offline field operations.

Vestibulum id ligula porta felis euismod semper. Nullam id dolor id nibh ultricies vehicula ut id elit. Integer posuere erat a ante venenatis dapibus posuere velit aliquet.
