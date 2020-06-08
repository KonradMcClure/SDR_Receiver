# SDR_Receiver
The goal of this project was to design a low-cost software-defined radio (SDR) receiver in a team of two. The SDR receiver connects to an antenna and covers the filtering, mixing, and amplification of the desired signal. It then connects a sound card within a computer and software handles the demodulation of the signal into playable audio. The mixing is handled using an Arduino Nano running code provided to us by Professor Frohne, which interfaces with Quisk, an SDR radio program that controls the receiver through the Arduino. Using Quisk, the radio can be tuned to a certain frequency by adjusting the speed of the local oscillator used in the mixing process. Our budget on this project to keep with the low-cost aspect was about $30 including fabrication of the PCB and the required components. 

# Table of Contents
- Theory
- Rev 2 Design
  - Bandpass Filter
  - DC Voltage Offset
  - Tayloe Mixer
    - Demultiplexer
    - Instrumentation Amplifiers
  - Lowpass Filter + Output
  - Voltage Smoother
- Simulation
- PCB Design
- Construction and Testing
- Revisions
- Credits

# Theory
![Circuit Block Diagram](/images/Diagrams/SDRReceiverBlockDiagram.png)
The basics of the SDR Radio are shown above in the block diagram. An antenna picks up the radio signal and passes it through a bandpass filter. This filter will attenuate any signals outside our desired range of 5 – 10 MHz. This is then passed through a Tayloe Mixer, also known as the Tayloe Quadrature Product Detector. The Tayloe Mixer is a simple and efficient mixer that uses a 1:4 demultiplexer and operational amplifiers to generate the I and Q quadrature signals for demodulation. After the signal has been split into four bandbase signals, they are amplified and combined into the I and Q quadrature signals. Those signals go through a final Low-Pass filter that will attenuate signals above 100KHz in frequency, effectively smoothing the output signal. This signal is then sent via 3.5mm audio cable to the soundcard which demodulates the signal. Quisk can then read the input from the sound card and play it. Quisk also interfaces to the Arduino Nano to set the speed of the local oscillator for tuning into the desired frequency.

# Rev 2 Design
This page is also acting as a report of the design we had for our class project. If the design is continued on in the future, I will likely move this description to the Wiki and update it with the new one.

## Bandpass Filter
![Bandpass Filter Rev2](/images/Schematics/Rev2_Schem_BandpassFilter.png)

The bandpass filter is a 3rd-Order, Series-First, Bessel filter that was generated using the RF Tools LC Filters Design Tool. It is designed to have input and output impedances of 50 Ω. The motivation to use a series-first configuration came from the capacitor C2 acting as a blockade for any DC signal coming in through the antenna. This would allow us better control over the DC voltage offset that is later required for our single-supply op-amps. 

## DC Voltage Offset
![DC Voltage OFfset Rev 2](/images/Schematics/Rev2_Schem_DCOffset.png)

Since we didn't want to create a negative supply for our op-amps, we opted for using them with a single supply and giving our signal a DC offset that sits in the middle between the 4.3V supply and ground. This ofset was done via a simple voltage divider of two 10k resistors from 4.3V to GND.

## Tayloe Mixer
![Tayloe Mixer Rev 2](/images/Schematics/Rev2_Schem_TayloeMixer.png)

The Tayloe Mixer is a mixer design as seen in [this paper](http://www.norcalqrp.org/files/Tayloe_mixer_x3a.pdf) by Dan Tayloe. We chose it due to it's relatively simple and elegant design. It utilizes a [two-channel SN74CBT multiplexer](http://www.ti.com/lit/ds/symlink/sn74cbt3253.pdf?ts=1591655665924) and a pair of [INA 821ID single gain resistor instrumentation amplifiers](http://www.ti.com/lit/ds/symlink/ina821.pdf?HQS=TI-null-null-mousermode-df-pf-null-wwe&DCM=yes&ref_url=https%3A%2F%2Fwww.mouser.com%2F&distId=26). To be more specific, Tayloe describes this as a "swtiching integrator," rather than a mixer, since a mixer normally produces both a sum and difference frequency of the RF and LO signals, while this design only produces the difference frequency. Acknowledging this, I am going to refer to it as a mixer for simplicity.
### Multiplexer
The SN74CBT multiplexer acts as a [product detector](https://en.wikipedia.org/wiki/Product_detector), converting the RF signal into four baseband signals. The switching speed of the MUX is set by the local oscillator, which is a divide by 4 circuit created from a 2-bit Johnson counter seen below. This speed is set to be the RF frequency 

### Instrumentation Amplifiers

## Low-Pass Filter
![Lowpass Filter](/images/Schematics/Rev2_Schem_LowpassFilter.png)

## Voltage Smoother and Analog Ground Reference
![Voltage Smoother Rev2]((/images/Schematics/Rev2_Schem_VoltageSmoother.png)

Professor Rob Frohne suggested that we all use a BJT to smooth the voltage coming from the computer, since all the rapid switching that happens in computers tends to create a lot of noise. He measured the output voltage to be about 4.3V based on the design on [his GitHub](https://github.com/frohro/IQ_SDR) , so we followed his example and designed ours the same way.

# Simulation
LTspice simulation info...

# PCB Design
![Board Render](/images/BoardRender_Rev2.png)

Designed on a 100mm x 100mm Board...


# Construction and Testing
The PCB layout we decided to print was my partner's who had a layout with really nice divisions between sections, which we believed would be easier for testing. You can find that layout on [his GitHub page](https://github.com/froeca/Software-Defined-Radio). All of the components are exactly the same, despite the different layout.

# Testing 


# Revisions
## Bandpass Filter Fix
![Corrected Bandpass Filter](/images/CorrectedBandpassFilter.png) 
![Corrected Filter Simulation](/images/CorrectedFilterSim.png)

Somewhere along the way, we made some mistake in our generation of the bandpass filter, which resulted in a response similar to what we were looking for but did not attenuate the high frequencies well. The above design has been corrected for cutoffs at 5MHz and 10MHz.
Somewhere along the way, we made some mistake in our generation of the bandpass filter, which resulted in a response similar to what we were looking for but did not attenuate the high frequencies well. The above design has been corrected for cutoffs at 5MHz and 10MHz.



## 
# Credits
Caleb, Dr. Frohne...
