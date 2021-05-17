# Radar Target Generation and Detection

## Overview

- Configure the FMCW waveform based on the system requirements.
- Define the range and velocity of target and simulate its displacement.
- For the same simulation loop process the transmit and receive signal to determine the beat signal
- Perform Range FFT on the received signal to determine the Range
- Towards the end, perform the CFAR processing on the output of 2nd FFT to display the target.

## Steps

- Define the radar specification

```
%% Radar Specifications
%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Frequency of operation = 77GHz
% Max Range = 200m
% Range Resolution = 1 m
% Max Velocity = 100 m/s
%%%%%%%%%%%%%%%%%%%%%%%%%%%
```

- Target specifications

```
%% User Defined Range and Velocity of target
% define the target's initial position and velocity. Note : Velocity
% remains contant

target_pos = 100;
target_vel = 60;
```

- FMCW waveform generation

```
% Design the FMCW waveform by giving the specs of each of its parameters.
% Calculate the Bandwidth (B), Chirp Time (Tchirp) and Slope (slope) of the FMCW
% chirp using the requirements above.

B_sweep = c/(2*Rres);
T_chirp = 5.5*2*Rmax/c;
alpha = B_sweep/T_chirp;
```
