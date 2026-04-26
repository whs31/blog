+++
title = "QuaSAR"
description = "High-Performance Software Package for Synthetic Aperture Radar Imagery"
date = 2026-04-20
template = "project.html"

[extra]
skills = ["DSP", "Geospatial", "Rust", "CUDA", "C++", "Python"]
slides = [
  {src = "/img/quasar-3.jpg",         type = "image"},
  {src = "/img/quasar-2.jpg",         type = "image"},
  {src = "/img/quasar-1.jpg",         type = "image"},
  {src = "/img/quasar-4.jpg",         type = "image"},
  {src = "/img/quasar-5.jpg",         type = "image"},
]
+++

## Overview

**QuaSAR** is a software package for synthetic aperture radar (SAR) imaging that combines the strict typing and memory safety of the Rust language with the computing power of NVIDIA CUDA parallel architectures.

The main task of the QuaSAR software package is to transform raw signals (holograms) into detailed georeferenced images or video streams.

## Features

### Telescopic Imaging

In telescopic mode, the image is formed within the antenna beam pattern.
This achieves maximum resolution, but the image coverage is limited to the surface area captured by the radar's antenna beam.

![Quasar telescopic](/img/quasar-telescopic.png)

### Strip Imaging

The image is formed continuously as the carrier moves ("stitched" together from strips).
This mode provides wide coverage of the ground surface along the entire route while maintaining stable image quality.

![Quasar strip](/img/quasar-3.jpg)

### Autofocus

Due to navigation errors and turbulence, images formed using the nominal platform velocity may appear out of focus. QuaSAR implements **entropy-minimization-based autofocus**.

The algorithm iterates over several reference velocity options within a range of 60–140% of the navigation value. For each velocity value, a SAR image is formed in the vicinity of a pre-selected point reflector, and the entropy of the sector is then calculated.
The entropy minimum corresponds to the best focus. The optimal velocity found is used when generating the final image.

![Quasar autofocus comparison](/img/quasar-autofocus-en.png)
![Quasar autofocus chart](/img/quasar-autofocus-chart-en.png)

### Video Stream

Video mode is implemented as frame-by-frame synthesis of telescopic SAR images with temporal sampling: each frame is independently synthesized from a sliding time window of the hologram. The result is encoded into **H.265/HEVC** using FFmpeg, with configurable quality parameters (CRF 18–28) and a frame rate of up to 120 frames per second.

Frame generation and saving are parallelized using a two-thread pipeline with non-blocking `crossbeam` channels, eliminating thread idle time while waiting for disk writes.

<video src="/video/quasar-video.mp4" controls muted playsinline autoplay loop style="width:100%;border-radius:8px"></video>

## Object Detection
The software package includes an object detection module based on the **YOLO11 OBB** neural network. Unlike standard detectors, the OBB model accounts for the rotation angle of objects, which is critically important for radar imagery.

The detection module automatically labels objects in the SAR image (e.g., equipment, vehicles, structures).

![Quasar yolo](/img/quasar-yolo.png)

## Architecture

The signal processing workflow is a multi-stage pipeline.

Key processing stages:
- **Signal Reading**: parsing the binary stream using `mmap`, synchronization pulse detection, and time window extraction
- **Range Compression**: short-time Fourier transform performed on CPU or CUDA cores
- **Navigation Parameter Calculation**: computation of key navigation parameters (drift angle, velocity) from signal characteristics using the Levenberg–Marquardt method
- **BPA**: the main computational node performing back-projection of the signal. The CUDA implementation of the algorithm includes the following low-level optimizations:
  - *Zero-allocation*: the algorithm does not allocate memory outside the main image buffer. cuFFT plans and intermediate buffers are cached;
  - *NVIDIA Intrinsics*: NVIDIA CUDA hardware intrinsics are used for trigonometric operations and type conversion;
  - *Global Memory Access Optimization*: the algorithm uses the `__ldg` intrinsic for fast global GPU memory access;
  - *Asynchronous Execution*: using `cudaMemcpyAsync` allows computation to overlap with data transfer to GPU memory.
- **Focusing**: phase error compensation using the image entropy minimization method
- **Visualization and Rendering**: dynamic contrast enhancement using adaptive histogram equalization and optional MP4 (H.265) video stream multiplexing

## Supported Platforms
The software package is designed as a cross-platform solution with an emphasis on embedded systems and runs successfully on a wide range of devices.

##### NVIDIA Jetson Orin Family

The target platform of the package. The project is optimized for NVIDIA modules, delivering maximum performance per watt for field SAR data processing. Supported models include:
- **Jetson Orin AGX**
- **Jetson Orin NX**
- **Jetson Orin Nano**

*Features*: full hardware acceleration support via NVIDIA CUDA (`sm_87`/`compute_87` architecture) and the use of specialized codecs for video stream multiplexing.

##### Single Board Computers (SBCs)

Despite the lack of hardware acceleration on these platforms, the high efficiency of parallel CPU computing allows the software package to run on SBCs such as the **Raspberry Pi 4/5**.

##### Desktop
- **Linux (x86_64)**: the primary development and analysis environment. On PCs with discrete NVIDIA GPUs (Ampere, Blackwell architectures), the maximum SAR image formation speed is achieved.

#### Platform Comparison
The comparison uses the formation time of a strip SAR image of a terrain area measuring 3,000 × 4,529 m.
| Platform                         |   Mode              |   Execution Time   |
| --------------------------------- | -------------------- | -------------------- |
| **Jetson Orin AGX**                   |   GPU (CUDA)         |   <span class="perf-fast">35.2 s</span>   |
| **Jetson Orin NX**                    |   GPU (CUDA)         |   <span class="perf-mid">48.1 s</span>   |
| **Jetson Orin Nano**                  |   GPU (CUDA)         |   <span class="perf-mid">48.3 s</span>   |
| **PC (RTX 5070, AMD Ryzen 5 7600)**   |   GPU (CUDA)         |   <span class="perf-fast">14.0 s</span>  |
| **PC (RTX 5070, AMD Ryzen 5 7600)**   |   CPU (12 threads)   |   <span class="perf-mid">39.8 s</span>   |
| **Raspberry Pi 5**                    |   CPU (4 threads)     |   <span class="perf-slow">116.5 s</span> |

## Stack
The project is written in **Rust** (Edition 2024). To achieve maximum performance in signal processing, **CUDA 12** is used, integrated via C FFI.

#### Libraries Used
##### Signal Processing & Mathematical Computing
- [**NVIDIA cuFFT**](https://developer.nvidia.com/cufft)<img class="lib-icon" src="/img/icons/nvidia-logo.png"> -- high-performance library for performing FFT on CUDA cores;
- [**rustfft**](https://github.com/ejmahler/RustFFT) and [**realfft**](https://github.com/HEnquist/realfft) -- efficient CPU FFT implementations supporting *AVX* and *SSE4.1* (x86_64) and *NEON* (ARM64) instruction sets;
- [**ndarray**](https://github.com/rust-ndarray/ndarray) and [**nalgebra**](https://github.com/dimforge/nalgebra)<img class="lib-icon" src="/img/icons/nalgebra-logo.png"> -- multi-dimensional array handling and linear algebra.

##### Concurrent Programming
- [**crossbeam**](https://github.com/crossbeam-rs/crossbeam) -- a set of tools for implementing multithreading;
- [**rayon**](https://github.com/rayon-rs/rayon) -- a library implementing work-stealing parallelism.

##### OS APIs
- [**jemallocator**](https://github.com/jemalloc/jemalloc) -- high-performance memory allocator minimizing fragmentation under intensive workloads;
- [**memmap2**](https://github.com/RazrFalcon/memmap2-rs) -- file-to-RAM mapping (`mmap`), enabling efficient processing of large holograms.

##### Data Handling & Formats
- [**GDAL**](https://gdal.org)<img class="lib-icon" src="/img/icons/gdal-logo.png"> -- library for reading and writing geospatial raster data;
- [**memchr**](https://github.com/BurntSushi/memchr) -- optimized pattern searching within holograms;
- [**serde**](https://github.com/serde-rs/serde) -- general-purpose data serialization/deserialization framework.

##### Visualization & Analysis
- [**OpenCV**](https://opencv.org/)<img class="lib-icon" src="/img/icons/opencv-logo.png"> -- open-source computer vision library;
- [**plotters**](https://github.com/plotters-rs/plotters) -- library for plotting graphs and charts;
- [**FFmpeg**](https://ffmpeg.org/)<img class="lib-icon" src="/img/icons/ffmpeg-logo.png"> -- universal multimedia processing toolkit.

For profiling and identifying bottlenecks, [**Tracy**](https://github.com/wolfpld/tracy.git) is used.
![Tracy profiler](/img/quasar-tracy.png)
![Cargo flamegraph](/img/quasar-flamegraph.png)
