# SDR_Receiver
The goal of this project was to design a low-cost software-defined radio (SDR) receiver in a team of two. The SDR receiver connects to an antenna and covers the filtering, mixing, and amplification of the desired signal. It then connects a sound card within a computer and software handles the demodulation of the signal into playable audio. The mixing is handled using an Arduino Nano running code provided to us by Professor Frohne, which interfaces with Quisk, an SDR radio program that controls the receiver through the Arduino. Using Quisk, the radio can be tuned to a certain frequency by adjusting the speed of the local oscillator used in the mixing process. Our budget on this project to keep with the low-cost aspect was about $30 including fabrication of the PCB and the required components. 

# Table of Contents
toc...

# Theory
![Circuit Block Diagram](/images/SDRReceiverBlockDiagram.png)
The basics of the SDR Radio are shown above in the block diagram. An antenna picks up the radio signal and passes it through a bandpass filter. This filter will attenuate any signals outside our desired range of 5 – 10 MHz. This is then passed through a Tayloe Mixer, also known as the Tayloe Quadrature Product Detector. The Tayloe Mixer is a simple and efficient mixer that uses a 1:4 demultiplexer and operational amplifiers to generate the I and Q quadrature signals for demodulation. After the signal has been split into four bandbase signals, they are amplified and combined into the I and Q quadrature signals. Those signals go through a final Low-Pass filter that will attenuate signals above 100KHz in frequency, effectively smoothing the output signal. This signal is then sent via 3.5mm audio cable to the soundcard which demodulates the signal. Quisk can then read the input from the sound card and play it. It also interfaces to the Arduino Nano to set the speed of the local oscillator for tuning into the desired frequency.

# Simulation
LTspice simulation info...

# Rev 2 Design
This page is also acting as a report of the design we had for our class project. If the design is continued on in the future, I will likely move this description to the Wiki and update it with the new one.

## Bandpass Filter
The bandpass filter is a 3rd-Order, Series-First, Bessel filter that was generated using the RF Tools LC Filters Design Tool. It is designed to have input and output impedances of 50 Ω. The motivation to use a series-first configuration came from the capacitor C2 acting as a blockade for any DC signal coming in through the antenna. This would allow us better control over the DC voltage offset that is later required for our single-supply op-amps. 

## Voltage Smoother and Analog Ground Reference

Professor Rob Frohne suggested that we all use a BJT to smooth the voltage coming from the computer, since all the rapid switching that happens in computers tends to create a lot of noise. He measured the output voltage to be about 4.3V based on the design on [his GitHub](https://github.com/frohro/IQ_SDR) , so we followed his example and designed ours the same way.

## DC Voltage Offset
Since we didn't want to create a negative supply for our op-amps, we opted for using them with a single supply and giving our signal a DC offset that sits in the middle between the 4.3V supply and ground. This ofset was done via a simple voltage divider of two 10k resistors from 4.3V to GND.

## Tayloe Mixer

## Instrumentation Amplifiers

## Low-Pass Filter

## Output

# PCB Design
![Board Render](/images/BoardRender_Rev2.png)

Designed on a 100mm x 100mm Board...


# Construction and Testing
The PCB layout we decided to print was my partner's who had a layout with really nice divisions between sections, which we believed would be easier for testing. You can find that layout on [his GitHub page](https://github.com/froeca/Software-Defined-Radio). All of the components are exactly the same, despite the different layout.

# Testing 


# Revisions
## Bandpass Filter Fix
![Corrected Bandpass Filter](/images/CorrectedBandpassFilter.png) ![Corrected Filter Simulation](/images/CorrectedFilterSim.png)
Somewhere along the way, we made some mistake in our generation of the bandpass filter, which resulted in a response similar to what we were looking for but did not attenuate the high frequencies well. The above design has been corrected for cutoffs at 5MHz and 10MHz.
Somewhere along the way, we made some mistake in our generation of the bandpass filter, which resulted in a response similar to what we were looking for but did not attenuate the high frequencies well. The above design has been corrected for cutoffs at 5MHz and 10MHz.



## 
# Credits
Caleb, Dr. Frohne...
