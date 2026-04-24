+++
title = "QuaSAR"
description = "High-performance synthetic aperture radar firmware, which converts raw signals into high-fidelity georeferenced imagery and videos"
date = 2026-04-20
template = "project.html"

[extra]
skills = ["DSP", "Geospatial", "Rust", "CUDA", "C++", "Python"]
slides = [
  {src = "/img/quasar-1.jpg",         type = "image"},
  {src = "/video/quasar-demo.mp4",    type = "video"},
  {src = "/img/quasar-3.jpg",         type = "image"},
]
+++

## Overview

Quasar is a processing pipeline and visualization platform for Synthetic Aperture Radar (SAR) imagery. SAR sensors penetrate cloud cover and operate independently of daylight, making them invaluable for persistent Earth observation. Quasar handles the full chain from raw complex-valued radar data to analysis-ready raster products.

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Curabitur vitae sem quam. Suspendisse potenti. Nullam vitae mauris accumsan, dignissim nisl non, lobortis ligula.

## Features

### Imagery Ingestion

Raw SAR scenes arrive as complex-valued I/Q data. Quasar ingests Sentinel-1 GRD and SLC products, performs radiometric calibration, and reprojects scenes to a common UTM grid. Nullam vulputate risus eget feugiat ornare. Cras nec erat quis ligula tristique faucibus vel sit amet nisi.

### Coherence & Change Detection

Interferometric coherence maps highlight areas of surface change between two acquisition dates. Donec sed odio dui. Donec ullamcorper nulla non metus auctor fringilla. Vestibulum id ligula porta felis euismod semper.

### Speckle Reduction

SAR imagery exhibits characteristic granular noise (speckle) due to coherent illumination. Quasar applies adaptive Lee and refined Lee filters to suppress speckle while preserving structural edges. Cras mattis consectetur purus sit amet fermentum. Cras justo odio, daccumulan eget facilisis at.

## Technical Stack

The processing core is written in **C++17** with GDAL for raster I/O and FFTW for frequency-domain operations. Analysis notebooks and automation scripts use **Python** (NumPy, Rasterio, Matplotlib). A lightweight Qt-based viewer provides interactive pan/zoom and band compositing.

Sed posuere consectetur est at lobortis. Aenean lacinia bibendum nulla sed consectetur. Vestibulum id ligula porta felis euismod semper. Nullam id dolor id nibh ultricies vehicula ut id elit.
