# ADR-0001 --- Modular Architecture for the TC211 Camera

**Status:** Proposed\
**Date:** 2026-09-10

## Context

The TC211 camera project is based on a previously proven amateur CCD
design using the Texas Instruments TC211 sensor, discrete clock-driver
circuitry including a DS0026, analogue video processing, an ADC, and a
PC parallel-port interface.

The original design has historical and practical value because it is
known to have worked, but the intent of the current project is not
merely to reproduce the original camera.

Modern FPGA devices, ADCs, analogue components, digital interfaces, and
development tools allow the camera to be redesigned as a modular
experimental platform.

The project should support the TC211 initially while also providing an
architecture that can later be adapted to other CCD sensors.

## Decision

The camera will be divided into physically and logically separate
modules:

``` text
CCD HEAD
    │
    ├── TC211 sensor
    ├── temperature sensing
    └── minimum necessary local analogue circuitry
         │
         ▼
CLOCK DRIVER MODULE
    │
    ├── converts FPGA logic signals
    ├── generates required multi-level CCD clocks
    └── provides sensor-specific clock voltages
         │
         ▼
ANALOGUE / ADC MODULE
    │
    ├── low-noise video conditioning
    ├── gain and level translation
    └── modern high-resolution ADC
         │
         ▼
FPGA CONTROLLER
    │
    ├── CCD timing sequencer
    ├── ADC conversion timing
    ├── digital CDS / averaging
    ├── image buffering
    └── host communications
```

The **Nexys A7** will initially provide the FPGA platform.

Its Pmod connectors will be used as modular digital interfaces between
the FPGA and external camera electronics.

A likely initial allocation is:

``` text
JA     CCD clock-driver interface
JB     ADC interface
JC     housekeeping/control
JD     spare/future expansion
JXADC  analogue housekeeping measurements
```

The precise allocation is not yet fixed.

## CCD Clock Generation

The FPGA will generate **logical clock states**, not CCD voltage levels
directly.

For example, the TC211 SRG signal requires multiple voltage levels. The
FPGA will represent these symbolically, while the interchangeable
clock-driver board will translate those states into the required
physical voltages.

Conceptually:

``` text
FPGA logical state
        │
        ▼
SRG_LOW / SRG_MID / SRG_HIGH
        │
        ▼
clock-driver module
        │
        ▼
actual CCD voltages
```

This keeps CCD-specific high-voltage and multi-level clock generation
out of the FPGA board.

The known-working DS0026/discrete TC211 clock-driver circuit will
provide the first implementation.

TMS3473B devices have also been sourced and may later be used to
investigate an alternative integrated clock-driver implementation.

## Analogue Acquisition

The original ADC architecture will not constrain the new design.

A modern ADC will be used from the first operational version of the new
camera.

The current reference candidate is the **Analog Devices AD4003**, an
18-bit, 2 MSPS SAR converter.

This is not yet a final component selection.

The ADC will communicate with the FPGA through a simple synchronous
serial interface consisting principally of:

``` text
CNV
SCK
SDO
SDI/configuration
```

One Pmod connector is expected to provide more than sufficient digital
connectivity.

## Correlated Double Sampling

The design will initially investigate **digital correlated double
sampling** rather than automatically reproducing an older analogue CDS
circuit.

The ADC may take several samples from both the CCD reset/reference level
and the pixel signal level:

``` text
reference samples: R1 R2 R3 ...
signal samples:    S1 S2 S3 ...

R = average(reference samples)
S = average(signal samples)

pixel = R - S
```

The FPGA will perform averaging and subtraction.

Provision should also be made for diagnostic modes that preserve raw ADC
samples so the CCD waveform can be analysed directly.

Analogue CDS or a hybrid analogue/digital approach remains an option if
measurements show that it provides superior performance.

## Measurement-Driven Analogue Design

The analogue front end will not be finalised until the behaviour of the
TC211 has been measured.

A 4-channel, 200 MHz oscilloscope is available and will be used to
examine:

``` text
IAG
SRG
CCD/preamp video
sample/conversion timing
```

Measurements will be used to determine:

-   CCD video amplitude
-   reset feedthrough
-   settling time
-   optimum reference sampling interval
-   optimum signal sampling interval
-   noise levels
-   analogue gain requirements
-   suitable ADC input range

The ADC and amplifier design will therefore be based on measured sensor
behaviour rather than assumption.

## SystemVerilog Structure

The CCD timing engine and ADC interface will be separate HDL modules.

For example:

``` systemverilog
tc211_sequencer
ad4003_interface
```

The TC211 sequencer determines **when** a sample is required.

The ADC interface determines **how** the ADC conversion and serial
transfer occur.

This separation should allow either block to be replaced independently.

## Extensibility

The design should permit interchangeable clock-driver modules and sensor
heads.

Possible future modules include:

``` text
clock-drivers/
    tc211-ds0026/
    tc211-tms3473/
    icx453/
    kai11002/
```

The long-term intention is that the same FPGA acquisition architecture
can support increasingly complex CCD sensors without redesigning the
entire system.

## Consequences

The project becomes more complex than a direct reproduction of the
original camera, but the resulting system will be substantially easier
to experiment with, measure, modify, and reuse.

The original TC211 circuit remains an important reference implementation
rather than a constraint on the new design.

The architecture deliberately separates:

**sensor physics, clock-voltage generation, analogue acquisition, and
digital timing.**

That separation is the central architectural decision.
