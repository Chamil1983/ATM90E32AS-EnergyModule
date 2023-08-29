# ATM90E32AS-Energy Metering Module

The ATM90E32AS-EnergyModule can monitor the energy usage in your entire home in real time. It can easily be hooked up to an ESP8266 or ESP32 to wirelessly transmit energy usage data into a program like EmonCMS or ESPHome/Home Assistant. It can also be used to monitor solar power generation to keep track of how much power you are making.

![ATM90E32AS-EnergyModule](Images/Board Top 1.jpg)

#### With the ATM90E32AS-EnergyModule you can:

*   **Save Money!**
    *   See exactly how much money you're spending on energy in real time
    *   Find appliances that are using too much electricity
    *   Calculate energy usage for a single room to divide an energy bill fairly among roommates
*   **View & Gather Energy Data**
    *   View the energy usage of your entire home
    *   Track solar power generation (two units required)
    *   Calculate how much it costs to charge your electric vehicle
    *   Remote energy monitoring for vacation or rental properties
    *   Review and graph historical energy data
*   **Be Informed!**
    *   Independent of your power utility's meter
    *   Set up alerts for over or under usage
    *   Prevent surprises on energy bills
    *   View usage data in the EmonCMS Android or iOS apps
    *   Automate notifications with your home automation system like "send my phone a message when the dryer is done", or even "if I leave the house, and the oven is on, send me an alert" (programming required)
*   **Spend less on energy monitoring hardware!**
    *   Affordable but very accurate
    *   Save hundreds over popular monitoring systems

## Contents

<!-- toc -->
- [Features](#features)
- [Hardware Setup](#hardware-setup)
- [Calibration](#calibration)

<!-- tocstop -->

## Features:

* **IC: MicroChip ATM90E32**
* **Connectivity**
     * SPI Interface to connect to any Arduino compatible MCU
     * Two IRQ interrupts, and one Warning output
     * Energy pulse output (pulses correspond to four LEDs)
     * Zero crossing output
* **Real Time Data Sampling**
     * Three current channels - up to 200A per channel
     * Three voltage channel 
     * Measurement Error: 1%
     * Dynamic Range: 6000:1
     * Gain Selection: Up to 4x
     * Voltage Reference Drift Typical (ppm/°C): 6
     * ADC Resolution (bits): 16
* **Calculates**
     * Active Power
     * Reactive Power
     * Apparent Power
     * Power Factor
     * Frequency
     * Temperature
* **Other Features**
     * Can use more than one at a time to measure as many circuits as you want, including solar power generation
     * Uses standard current transformer clamps to sample current
     * Includes built-in 1A 3v3 buck converter to power MCU board 
     * Compact size at only 40 mm x 50 mm

## What you'll need:

* Current Transformers for wire:
   * **13mm and less**, the [SCT-013-000 100A/50mA](https://www.amazon.com/SCT-013-000-Retractable-Current-Transformer-SCT013000/dp/B085KWXYPV/ref=sr_1_9?crid=2M8SKL3X58TF6&keywords=SCT-013-000&qid=1693307831&sprefix=sct-013-000%2Caps%2C309&sr=8-9&th=1) has a 13mm opening for wire.
   * **13.5mm - 16mm - 4/0 AWG** (usually aluminum, 200A service) not thicker than 16mm with insulation, we recommend the [YHDC SCT-016 120A/40mA](https://www.amazon.com/YHDC-current-transformer-SCT016-1/dp/B01CVBHSGO). 
   
   ![SCT016](/Images/current_transformer_SCT016.jpg)
   
   * **Greater than 16mm**, the [Magnelab SCT-0750-100](https://amzn.to/2IF8xnY) (must sever burden resistor connection on the back of the board since they have a built in burden resistor). These have wire leads and will need an adapter or screw connectors soldered to the energy meter board.
   
   ![JP1-JP2](/images/energy_meter_JP1-JP2-JP3.jpg)
   
   * Other CTs can also be used as long as they're rated for the amount of power that you are wanting to measure and have a current output no more than 720mA. For safety, they MUST have a built in Zener diode or burden resistor. 
* AC Transformer: [Jameco Reliapro 9v](https://amzn.to/2XcWJjI)
* An [ESP32](https://amzn.to/2pCtTtz), ESP8266, LoRa, or anything else that has an SPI interface.
* Jumper wires with Dupont connectors, or perf board to connect the two boards. The energy meter kit comes with a PCB adapter for an ESP32.
* The software located here to load onto your controller
* [EmonCMS](https://emoncms.org/site/home), ThingSpeak, InfluxDB/Grafana, or similar
* The latest version of EmonCMS IoT can be download here or goto [EmonCMS](https://github.com/openenergymonitor/EmonESP/releases)



## Hardware Setup

### Connect your controller to the energy meter
Connect the pins on the Energy Monitor to your MCU. If you have the adapter board, everything should already be connected properly. The following are the default for each, but they can be changed in software if you are using these pins for something else. 

For the ESP32:
*   5 - CS
*   18 - CLK
*   19 - MISO
*   23 - MOSI

For the ESP8266:
*   D8/16 - CS
*   D5/14 - CLK
*   D6/12 - MISO
*   D7/13 - MOSI

***For other Ardino boards / MCUs use related ISP(Hardwre/User defined) pins acording to datasheet 

Don't forget to hook up the 3V3 and GND pins! 

**The energy meter can supply up to 500mA of 3.3v power to your controller**, so no other external power source should be needed. Some ESP32 dev boards may use more than 500mA when trying to initially connect to WiFi. If this is the case, you may not be able to connect to WiFi. If this happens, we recommend using another power source for the ESP32 - either a 5v DC adapter or a USB phone charger that outputs at least 500mA. It is **not** recommended to leave USB power plugged into an ESP at the same time as the energy meter's power 3V3 output. This can damage components. 

Other pins on your controller can be connected to the WARN and IRQ outputs. 

### Using more than one meter
More than one meter can be hooked up to a single MCU to monitor a solar grid, for example. 

To do this:
- Connect all CLK, MISO, and MOSI pins together
- Connect the GND and 3V3 from one meter to the MCU
- Connect the CS pin of one energy meter to an open GPIO on your MCU, and the CS pin on the second energy meter to another open GPIO on your MCU. These pins must be set in the software. See the [examples folder](/Software/examples) for the example with more than one energy meter.
- If you would like to monitor voltage from two sources, you will need two AC transformers. If only one voltage, you can split the output of 1 AC transformer using a 2.5mm DC jack Y-cable.

You can also [purchase a solar adapter here](https://circuitsetup.us/index.php/product/energy-meter-solar-esp32-adapter-2x-split-phase-meter-headers-space-for-spi-oled-screen/).


## Calibration

If you purchased a kit that came with current transformers **and** an AC Transformer, you should not have to calibrate anything unless you want to. If you are providing your own AC Transformer, you will need to calibrate the voltage.

The default configuration of the Energy Meter software is set to use the SCT-016 120A/40mA current transformers, and the [Jameco Reliapro 9v AC transformer](https://amzn.to/2XcWJjI). There are also values for 100A Magnalab, SCT-013 100A 50mV current transformers, and the 12v version of the AC transformer located in the **energy_meter.h** file, and in the EmonESP web interface. Simply change the values under CALIBRATION SETTINGS if you are using a 12v AC Transformer or the Magnalab current transformers.  **If you are using any of these you likely will not need to calibrate, but if you want to be sure your readings are the most accurate then calibration is recommended.** 

Alternatively, if you have equipment that can read active and reactive energy pulse outputs, CT1-CT4 pins can be used for this. It is recommended that these connections are opto-isolated to prevent interference. 

### For calibration you will need:
1.  A multi-meter, or to make it easier and safer, a [kill-a-watt](https://amzn.to/2TXT7jx) or similar. A clamp meter will also work if you would like to measure mains power directly.
2.  A hair dryer, soldering iron, electric heater, or anything else that uses a large amount of resistive current.
3.  A modified power cable that allows you to put a current transformer around only the hot (usually black) wire.

### Setup
1.  At this point all wires should be connected between your ESP and the Energy Monitor.
2.  Connect the Energy Monitor to the AC Transformer and plug it in - the ESP and Energy Meter should both have power. If either do not, check your connections.

#### With the Arduino IDE
1.  Open the Arduino IDE
2.  Connect your ESP to your computer via USB cable
3.  Select the COM port under Tools > Port
4.  Go to Tools > Serial Monitor
5.  Values should be scrolling by. If you do not see anything in the serial window, make sure the correct COM port is selected for your ESP in the Arduino IDE.


### Voltage Procedure 

1.  In the Serial Monitor window or in EmonESP under 4. Latest Data, view the value for Voltage - take note of this (if you are getting a value above 65k, something is not hooked up or working correctly)
2.  Take a reading of the actual voltage from an outlet in your house.  For the Kill-a-watt, just plug it in, and select voltage. Compare the values.
3.  Adjust the value for VoltageGain in energy_meter.h or EmonESP by calculating:

<pre>New VoltageGain = (your voltage reading / energy monitor voltage reading) * VOLTAGE_GAIN</pre>

VOLTAGE_GAIN is the value currently set for the voltage gain parameter in energy_meter.h.

Test again after adjusting the value and re-uploading the sketch to your ESP. If it is still off, do the procedure again, but replace VOLTAGE_GAIN with the last value used. 

If you are not located in the US and have 50hz power, change the Frequency value to 389

### Current Procedure
For calibrating **CurrentGainCT1 , CurrentGainCT2  & CurrentGainCT3**:

1.  In the Serial Monitor window or in EmonESP under 4. Latest Data, view the value for Current 
2.  If the value is negative, you are either exporting power (solar), or the current transformer is backwards.
3.  Compare what you are seeing for current from the Energy Monitor to the reading on the Kill-a-watt
4.  Adjust the value for CurrentGainCT1 or CurrentGainCT2 in energy_meter.h or EmonESP by calculating:

<pre>New CurrentGainCT1 or CurrentGainCT2 or CurrentGainCT3 = (your current reading / energy monitor current reading) * CURRENT_GAIN_CT#</pre>

CURRENT_GAIN_CT# is the value currently set for the current gain parameter in energy_meter.h.

Test again after adjusting the value and re-uploading the sketch to your ESP. If it is still off, do this again, but replace the CURRENT_GAIN_CT# with the last value used. It is possible that the two identical current sensors will have different CurrentGain numbers due to variances in manufacturing, but it shouldn't be drastic. Note that the positioning of the CT sensor on the hot wire can influence the current reading. 

For more details, see the Calibration Procedure in the [Microchip Application notes.](http://ww1.microchip.com/downloads/en/AppNotes/Atmel-46103-SE-M90E32AS-ApplicationNote.pdf)




