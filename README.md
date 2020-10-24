# TC211
Build a  'minimum hardware' camera based on TC211 CCD

## Introduction
Many years ago I built a [Cookbook Camera](http://www.wvi.com/~rberry/cookbook/cb245faq.htm). Having recently retired, I'd like to build another 
CCD camera using modern components.

Rather than jumping and buying a $5,000 CCD sensor (which I'd probable reduce to smoke on the first test), I'd thought I might try to redesign and 
rebuild the [Cookbook Camera](http://www.wvi.com/~rberry/cookbook/cb245faq.htm) using a micro controller and minimal support parts. Fortunately I still have
a spare [TC211 CCD chip](http://rogercortesi.com/portf/uec/downloads/TC211.pdf) in my workshop, so any stuff-up with the hardware will have little
impact on my bank account.

So here goes...

## Background reading

* [Old design notes](./cookbook_design_notes.pdf) for the Cookbook camera
* [TC211 datasheet](http://rogercortesi.com/portf/uec/downloads/TC211.pdf)
* [Photo Gallery](http://www.wvi.com/~rberry/cookbook/gallery2.htm)
* [TC211 still available](https://aliexpress.ru/item/32930364946.html?af=332184&utm_campaign=332184&aff_platform=portals-tool&utm_medium=cpa&afref=https%3A%2F%2Fimall.com%2F&dp=24b822c447953cdafdb1cfe956c993f0&cv=47843&pvid=06878be9-16a9-4b8f-8450-88b395658c71&mall_affr=pr3&sk=_ePNSNV&aff_trace_key=fb529ac63e5c45959720d5c6a7a131b9-1603417079698-08161-_ePNSNV&rmsg=do_not_replacement&scm=1007.23534.124736.0&terminal_id=183a578cf06c48158b226d4be8bfc7b4&utm_source=admitad&utm_content=47843)
* [Clock driver DS0026](https://www.jameco.com/Jameco/Products/ProdDS/16301.pdf) only gives two voltage levels -- find another driver
* [Timing considerations for TC211](https://www.ti.com/lit/an/soca011/soca011.pdf?ts=1603413648448)
* [Application notes for MC34063](https://www.ti.com/lit/an/slva252b/slva252b.pdf?ts=1603150979912)
* [Evaluation board for TC211](https://www.ti.com/lit/ug/socu005/socu005.pdf?ts=1603521955801&ref_url=https%253A%252F%252Fwww.ti.com%252Fsitesearch%252Fdocs%252Funiversalsearch.tsp%253FsearchTerm%253Dccd%2Bvertical%2Bdriver) -- contains very useful voltage and timing information


## Main components

* MCU -- based on [STM32F103C8T6 microcontroller](https://www.st.com/resource/en/datasheet/stm32f103c8.pdf)
* CCD -- [TC211](http://rogercortesi.com/portf/uec/downloads/TC211.pdf)
* Power supply (Custom circuit -- 12V in -- based on [MC34063](https://www.onsemi.com/pub/Collateral/MC34063A-D.PDF))
* Clock driver -- Software synthesised clocks from MCU -- hardware level shifting
* ADC -- use the microcontroller ADC (perhaps an op-amp/buffer will be necessary)
* Computer interface -- USB
* Temp controll -- nope
* Shutter -- necessary, so lets do some research

## Performance specs

* 192 x 165 pixels
* 12 bit ADC resolution (64KB image size)
* Readout -- 4.5 secs at 115000 Baud (we can do better than that!)

