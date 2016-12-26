---
layout: post
comments: true
categories: Others

img: /images/car.png
abstract: "An intelligent car project. The car is able to track ping-pong ball and capture the ball."
tags: raspberry-pi FPGA arduino open-cv

---

> When I was a junior, I made an intelligent car which is able to track ping-pong ball and capture the ball. I used RaspberryPi as the image processing center and controlled the car by Arduino.

## Introduction

* **Raspberry Pi**
The Raspberry Pi is a series of credit card–sized single-board computers developed in Wales, United Kingdom by the Raspberry Pi Foundation with the intention of promoting the teaching of basic computer science in schools and developing countries. The original Raspberry Pi and Raspberry Pi 2 are manufactured in several board configurations through licensed manufacturing agreements with Newark element14 (Premier Farnell), RS Components and Egoman. The hardware is the same across all manufacturers.

* **Arduino**
Arduino is an open-source computer hardware and software company, project and user community that designs and manufactures microcontroller-based kits for building digital devices and interactive objects that can sense and control objects in the physical world.

## Implementation

I tracked the ping-pong ball by Open-CV based on its color and shape. The image-processing is ran on Raspberry Pi and the car is controlled by instructions sent from Raspberry Pi to Ardunio.

## Exhibition

![exhibition](./exhibition.png)