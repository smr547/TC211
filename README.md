# TC211
Build a  'minimum hardware' camera based on TC211 CCD

## Introduction
Many years ago I built a [Cookbook Camera](http://www.wvi.com/~rberry/cookbook/cb245faq.htm). Having recently retired, I'd like to build another 
CCD camera using modern components.

Rather than jumping a buying a $5,000 CCD sensor (which I'd probable reduce to smoke on the first test), I'd thought I might try to redesign and 
rebuild the [Cookbook Camera](http://www.wvi.com/~rberry/cookbook/cb245faq.htm) using a micro controller and minimal support parts. Fortunately I still have
a spare [TC211 CCD chip](http://rogercortesi.com/portf/uec/downloads/TC211.pdf) in my workshop, so any stuff-up with the hardware will have little
impact on my bank account.

So here goes...

## Background reading

* [Old design notes](./design_notes.pdf) for the Cookbook camera
* [TC211 datasheet](http://rogercortesi.com/portf/uec/downloads/TC211.pdf)
* [Photo Gallery](http://www.wvi.com/~rberry/cookbook/gallery2.htm)


## Main components

* MCU -- based on [STM32F103C8T6 microcontroller](https://www.st.com/resource/en/datasheet/stm32f103c8.pdf)
* CCD -- [TC211](http://rogercortesi.com/portf/uec/downloads/TC211.pdf)
* Power supply (Custom circuit -- 12V in)
* Clock driver -- Software synthesised clocks from MCU -- hardware level shifting
* ADC -- use the microcontroller ADC (perhaps an op-amp/buffer will be necessary)
* Computer interface -- USB
* Temp controll -- nope
* Shutter -- necessary, so lets do some research

## Performance specs

* 192 x 165 pixels
* 12 bit ADC resolution (64KB image size)
* Readout -- 4.5 secs at 115000 Baud (we can do better than that!)

