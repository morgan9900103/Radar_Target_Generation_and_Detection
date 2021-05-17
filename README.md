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

Design the FMCW waveform by giving the specs of each of its parameters.
```
% Calculate the Bandwidth (B), Chirp Time (Tchirp) and Slope (slope) of the FMCW
% chirp using the requirements above.

B_sweep = c/(2*Rres);
T_chirp = 5.5*2*Rmax/c;
alpha = B_sweep/T_chirp;
```
Then, we need simulate the signal propagation and move target scenario.
```
% For each time sample we need update the transmitted and
% received signal.
Tx(i) = cos(2 * pi * (fc*t(i) + alpha*t(i)^2/2));
Rx(i) = cos(2 * pi * (fc*(t(i) - td(i)) + alpha*(t(i) - td(i))^2/2));
Mix(i) = Tx(i).*Rx(i);
```

- Range and Doppler measurement

2st FFT will generate a Range Doppler Map as seen in the image below and it will be given by variable ‘RDM’.


- CFAR implementation

The 2D CFAR is similar to 1D CFAR, but is implemented in both dimensions of the range doppler block. The 2D CA-CFAR implementation involves the training cells occupying the cells surrounding the cell under test with a guard grid in between to prevent the impact of a target signal on the noise estimate.

1. Determine the number of Training cells for each dimension. Similarly, pick the number of guard cells. Here I set T_range = 12, T_doppler = 12, G_range = 3, G_doppler = 3.
2. Slide the cell under test across the complete matrix. Make sure the CUT has margin for Training and Guard cells from the edges.
3. For every iteration sum the signal level within all the training cells. To sum convert the value from logarithmic to linear using db2pow function.
4. Average the summed values for all of the training cells used. After averaging convert it back to logarithmic using pow2db.
5. Further add the offset to it to determine the threshold. I set offset to 8 in this case.
6. Next, compare the signal under CUT against this threshold.
7. If the CUT level > threshold assign it a value of 1, else equate it to 0.
