# Dual-Polarity-20V-DC-DC-Supply
Created a 4 layer PCB with adjustable positive and negative buck converter rail with up to 1A output capability.
Originally when establishing this project I had several goals and accomplishments for this project:
1. Achieve an adjustable +/- 20V design via a potentiometer
2. Have a peak to peak ripple voltage of less than 200mV under a 1A load
3. Create a board that can be used for transistors, operational amplifiers, and class A-D amplifiers
4. Keep all components on the board surfacemount and a reasonable size (less than 8x8 dimensions in inches)

Quick spec summary:
Input voltage: 25V DC (From WANPTEK DPS3010U power supply)
Non-inverting rail: LMR33640ADDAR
Inverting Rail: LM5088MH-1/NOPB
E-Fuse: TPS16416DRCR
Shunt Regulator: TL431BQDBZR
Topology: Synchronous Buck (Positive Rail) / Non-Synchronous Buck-Boost (Negative Rail)


When first creating this design I had the idea of using my WANPTEK DPS3010U 30V 10A power supply with a 25V input via banana jack connectors and output that could generate a positive and negative rail via two separate ICs. 

My first iteration of the schematic used the LMR33640ADDAR for both rails (one for each). I selected this part due to its high input voltage capability (36V), switching frequency option of 400kHz and 1MHz, and maximum current output of 4A. Given that Texas Instruments has an application note on this IC for working in the invertinng buck boost topology it seemed to be the perfect component. However upon further investigation, the -VOUT rail tied to the VIN rail at the input pin caused the voltage range I was workign with to exceed the ICs maximum voltage rating (25V in and -20V out at max output results in 45V which exceeds the 36V rating). This idea was then quickly scrapped as althoguh I don't plan on using -20V often for my breadboard circuits it would be a nice feature to have. 

Back to the drawing board I went, ultimately having a tough time finding a buck converter that could not only have some headroom past a 25V input, but also where the combination of the positive input and absoloute value of the negative rail of 45V. Searching through digikey I found ICs that could provide that negative topololgy but didn't have the current or voltage capabilites I was looking for. 

Eventually I managed to find the LM2596HVS-ADJ-EV as it had a input and output maximum voltage of 57V, which would pass the 45V threshold along with a 3A output capability. However, upon further reading into the datasheet and implementing the layout other problems occured. The switching frequency was only 150kHz, requiring much larger capacitors and inductors to properly filter the rail, with even the eval board (LM2596ADPBCKGEVB) offering a rather clunky layout that took up unnecessary space. Since my LMR33640ADDAR was much more compact even with a 3A inductor and large ceramic output capacitors, this left a lot of empty and unused space on my PCB. This component seemed far from ideal as I wanted a part with at least a 550kHz switching frequency or an adjustable one, so I had to scrap the negative rail once again and start from scratch.

The part I finally decided to select was the LM5088MH-1/NOPB, it could withstand a supply voltage of 75V and due to it being a non-synchronous buck controller, the load current is not internally limited to the IC but rather the MOSFET, schottky diode, and inductor. I don't expect to use this board for a load current greater than 1A but given I wanted some headroom the schottky diode and inductor I selected for 3A capability with the MOSFET at 7A. For this buck converter I used Texas Instruments WEBENCH as a design reference for the inverting topology along with calculating values for the compensation network, inductance, feedback resistors, amongst others which can be seen in the spreadsheet section under calculations of this GitHub. 

After re-designing the negative rail for a third time I then assembled the PCB via a hot air station and soldering iron, then proceeded to test it. The negative rail was initially ringing after -9V and would drop signigicanyly when a load was attached to it. Upon scoping the compensatiion pin and the FB node I realized I had essentially caused extra noise in the system by adding in the capacitor in parrallel with the potentiometer for the feedback node. Removing this component from the board removed the noise entirely and the negative rail no longer significantly dropped when a load was attached. 

Several sections will be added to complete this project:
1. A link to the Youtube series I plan on filming (should be done by May)
2. A section explaining corrections I made to the design/mistakes I made
3. Scope captures of the input ripple, output ripple switching node for both rails, etc.
