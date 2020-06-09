# Low-Cost Software Defined Radio Receiver
The goal of this project was to design a low-cost software-defined radio (SDR) receiver. 
## Design Objectives
Our professor, [Dr. Rob Frohne](https://github.com/frohro), gave us the following design objectives:
  1. Minimum discernible signal less than 1 uV
  2. Good image rejection
  3. Low noise figure
  4. Inexpensive to build ($25 component budget)
  
My partner, [Caleb Froelich](https://github.com/froeca), and I set our own design objectives as well to help narrow down the design we settled on:
  1. **Simple Design**: We wanted our design to be easy to understand and construct. As such we avoided using transformers and used a Tayloe Mixer which has a relatively easy to understand design.
  2. **Ease of Construction**: Since this class took place during an online quarter due to COVID-19, we wanted to make sure construction wouldn't be too difficult without the usual tools we have in our lab. This decision shaped our board layout and part selection (picking through-hole parts as opposed to surface-mount when possible).
  3. **Ease and Quality of Amplification**: Most of the cost that goes into the board went into getting good, low-noise operational amplifiers. The instrumentation amps we chose use a single resistor to set the gain, making it easy to switch out and test different gain values.

## Overview
The Software Defined Radio (SDR) receiver connects to an antenna and covers the filtering, mixing, and amplification of the desired signal. It then connects a sound card within a computer and software handles the demodulation of the signal into playable audio. The mixing is handled using an Arduino Nano running code provided to us by our Professor, [Dr. Rob Frohne](https://github.com/frohro), which interfaces with [Quisk](https://james.ahlstrom.name/quisk/), an SDR that controls the receiver through the Arduino. Using Quisk, the radio can be tuned to a certain frequency by adjusting the speed of the local oscillator used in the mixing process. Our budget on this project to keep with the low-cost aspect was about $30 including fabrication of the PCB and the required components. 

# Theory
![Circuit Block Diagram](/images/Diagrams/SDRReceiverBlockDiagram.png)
The basics of the SDR Receiver are shown above in the block diagram, with the blue components being defined by software. An antenna picks up the radio signal and passes it through a bandpass filter. This filter will attenuate any signals outside our desired range of 5 – 10 MHz. This is then passed through a Tayloe Mixer, also known as the Tayloe Quadrature Product Detector. The Tayloe Mixer is a simple and efficient mixer that uses a 1:4 demultiplexer and operational amplifiers to generate the I and Q quadrature signals for demodulation. After the signal has been split into four bandbase signals, they are amplified and combined into the I and Q quadrature signals. Those signals go through a final Low-Pass filter that will attenuate signals above 100KHz in frequency, effectively smoothing the output signal. This signal is then sent via 3.5mm audio cable to the soundcard which demodulates the signal. Quisk can then read the input from the sound card and play it. Quisk also interfaces to the Arduino Nano to set the speed of the local oscillator for tuning into the desired frequency.

# Rev 2 Design
![Rev 2 Schematic](images/Schematics/Rev2_Schem.png)

Here is the full design schematic, which we'll break down piece by piece below. You can look at the full image [here](https://github.com/KonradMcClure/SDR_Receiver/blob/master/images/Schematics/Rev2_Schem.svg) or download the [KiCad](https://kicad-pcb.org/) project [here](https://github.com/KonradMcClure/SDR_Receiver/tree/master/SDRReciever) to take a closer look yourself.

This page is also acting as a report of the design we had for our class project. If the design is continued on in the future, I will likely move this description to the Wiki and update it with the new one.

## Bandpass Filter
![Bandpass Filter Rev2](/images/Schematics/Rev2_Schem_BandpassFilter.png)

The bandpass filter is a 3rd-Order, Series-First, Bessel filter that was generated using the RF Tools LC Filters Design Tool. It is designed to have input and output impedances of 50 Ω. The motivation to use a series-first configuration came from the capacitor C2 acting as a blockade for any DC signal coming in through the antenna. This would allow us better control over the DC voltage offset that is later required for our single-supply op-amps. 

## DC Voltage Offset
![DC Voltage OFfset Rev 2](/images/Schematics/Rev2_Schem_DCOffset.png)

Since we didn't want to create a negative supply for our op-amps, we opted for using them with a single supply and giving our signal a DC offset that sits in the middle between the 4.3V supply and ground. This ofset was done via a simple voltage divider of two 10k resistors from 4.3V to GND.

## Tayloe Mixer 
![Tayloe Mixer Rev 2](/images/Schematics/Rev2_Schem_TayloeMixer.png)

The Tayloe Mixer is a mixer design as seen in [this paper](http://www.norcalqrp.org/files/Tayloe_mixer_x3a.pdf) by Dan Tayloe. We chose it due to it's relatively simple and elegant design. It utilizes a [two-channel SN74CBT multiplexer](http://www.ti.com/lit/ds/symlink/sn74cbt3253.pdf?ts=1591655665924) and a pair of [INA821ID single gain resistor instrumentation amplifiers](http://www.ti.com/lit/ds/symlink/ina821.pdf?HQS=TI-null-null-mousermode-df-pf-null-wwe&DCM=yes&ref_url=https%3A%2F%2Fwww.mouser.com%2F&distId=26). To be more specific, Tayloe describes this as a "swtiching integrator," rather than a mixer, since a mixer normally produces both a sum and difference frequency of the RF and LO signals, while this design only produces the difference frequency. Acknowledging this, I am going to refer to it as a mixer for simplicity.

### Multiplexer
The [SN74CBT multiplexer](http://www.ti.com/lit/ds/symlink/sn74cbt3253.pdf?ts=1591655665924) acts as a [product detector](https://en.wikipedia.org/wiki/Product_detector), converting the RF signal into four baseband signals. The switching speed of the MUX is set by the local oscillator, which is a divide by 4 circuit created from a 2-bit Johnson counter seen below. This speed is set to be the RF frequency + 10.7kHz. The sampling capacitors shown between the MUX and instrumentation amps will follow the voltage level of the signal while its line's switch is active, and then hold that value while the switch is closed. This will occur on four different lines, outputting four bandbase signals at phases of 0°, 90°, 180°, and 270°. 

### Instrumentation Amplifiers
The [INA821ID Instrumentation Amplifiers](http://www.ti.com/lit/ds/symlink/ina821.pdf?HQS=TI-null-null-mousermode-df-pf-null-wwe&DCM=yes&ref_url=https%3A%2F%2Fwww.mouser.com%2F&distId=26) act as differencial summers of the 0° and 180° signals, and the 90° and 270° signals. This is because the information between the two signals within the two pairs are redundant to each other. Summing 0° and 180° will get us our In-Phase (I) signal, and summing the 90° and 270° signals will get us our Quadrature (Q) signal.

## Lowpass Filter
![Lowpass Filter](/images/Schematics/Rev2_Schem_LowpassFilter.png)

Now that we have our I and Q signals, we'll send them through a Lowpass filter that will attenuate the high frequencies that may still remain in the signal, effectively smoothing it out. For this we used an [OP213FPZ op amp](https://www.analog.com/media/en/technical-documentation/data-sheets/OP113_213_413.pdf), which has 2-channel produces low noise. We used the [Texas Instruments Filter Design Tool](https://webench.ti.com/filter-design-tool/) to generate this as a two-pole filter with a 100kHz cutoff and no gain.

## Voltage Smoother and Analog Ground Reference
![Voltage Smoother Rev2](/images/Schematics/Rev2_Schem_VoltageSmoother.png)

Professor Rob Frohne suggested that we all use a BJT to smooth the voltage coming from the computer, since all the rapid switching that happens in computers tends to create a lot of noise. He measured the output voltage to be about 4.3V based on the design on [his GitHub](https://github.com/frohro/IQ_SDR) , so we followed his example and designed ours the same way.

# Simulation
LTspice simulation info...

# PCB Design
![Board Render](/images/BoardRenderSmall_Rev2.png)

We sourced our board from [JLCPCB](https://jlcpcb.com/), who had an offer going of 5, 2-layer, 100x100mm boards for $2. With that size in mind, my partner and I both did our own board layouts and compared after to decide which to send in for printing. We wanted to be sure to match the impedence of the antenna all the way through to the instrumentation amps, so we used the PCB Calculator in KiCad to find the trace width we needed: 1.064mm. The input to the calculator can be seen here:
![PCB Calculator Screen](/images/PCBCalculatorSettings.png)

We got the rest of the information, such as the substrate parameters, from [JLCPCB's Capabilities page](https://jlcpcb.com/capabilities/Capabilities). The design shown above was mine, doing my best to keep traces relatively short and stright and parts close to the next. However...

# Construction and Testing
The PCB layout we decided to print was my partner's who had a layout with really nice divisions between sections, which we believed would be easier for testing. You can find that layout on [his GitHub page](https://github.com/froeca/Software-Defined-Radio). All of the components are exactly the same, only the layout is different.

Before receiving the parts from Dr. Frohne, JLCPCB, and Digikey, we put together a Construction and Testing plan which can be found in the documents folder of this repository. This plan wasn't followed exactly to the T during our assembly, but it was an important step for us thinking through the process and how to best assemble the sections so that we could test them in isolation before connecting them together.

# Testing
For info on the testing of the project, head over to the [Testing page on the Wiki](https://github.com/KonradMcClure/SDR_Receiver/wiki/Rev-2-Testing)!



# Credits
My teammate [Caleb Froelich](https://github.com/froeca), who has [his own repository of this project](https://github.com/froeca/Software-Defined-Radio).
My professor [Dr. Rob Frohne](https://github.com/frohro), who also has [his own repository of this project](https://github.com/frohro/IQ_SDR)!
