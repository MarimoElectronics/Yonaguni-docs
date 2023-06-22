# Yonaguni
## **Project Descriptions**
  Yonaguni is a Software Defined Radio (SDR) System on Module (SoM) for Analog Devices ADRV9002 Agile Transceiver™ IC.
  The SoM is an Intel Cyclone® V SE SoC FPGA-based single-board that can be used as an evaluation tool and prototyping platform.
  The ADRV9002 2×2 transceiver with integrated DPD engine operates from 30 MHz to 6 GHz and supports narrow-band (kHz) and wideband operation up to 40 MHz.
  Marimo Electronics Co., Ltd. can also support the development of custom versions optimized for specific applications. 




## **Hardware Overview**
  Yonaguni is a compact RF System on Module (SoM).
  Yonaguni integrates an Intel Cyclone® V SE SoC FPGA baseband processor, Analog Devices ADAU1761 SigmaDSP® integrated audio codec IC, and power circuits with Analog Devices ADRV9002 Agile Transceiver™ IC.
  ![Board Picture](./img/board.png)



### **Hardware Architecture**
  The following shows a block diagram of the Yonaguni hardware architecture.
  ![Functional block diagram](./img/functional_block.svg)
  For further descriptions, refer ```Yonaguni_HW-Specifications_r2.00.docx``` (current version is only available in Japanese).



### **ADRV9002 Connectors, Jumper, Switch**
#### **RF Ports (CN1 - CN7)**
| Connector No | Type | Mnemonic | Note |
----|----|----|----
| CN1 | In  | DEVCLK_IN | Device clock input |
| CN2 | Out | TX1_OUT   | Output for transmitter channel 1 (Tx1) |
| CN3 | In  | EXT_LO1   | External LO input 1 (LO1) * |
| CN4 | Out | TX2_OUT   | Output for transmitter channel 2 (Tx2) |
| CN5 | In  | EXT_LO2   |  External LO input 2 (LO2) * |
| CN6 | In  | RX1A_IN   | Input A for receiver channel 1 (Rx1) ** |
| CN7 | In  | RX2A_IN   | Input A for receiver channel 2 (Rx2) ** |

\* External LO feature is not supported in this release.

\** Current hardware design version showed frequency response degradation at 3.5 GHz and above.

#### **ADRV9002 Interface Selector (J1)**
  Switch between CMOS synchronous serial interface (CSSI) LVDS synchronous serial interface (LSSI).
  If the FPGA is configured with CSSI but 2.5V is applied, the red LED (D2) lights up and indicates the error.
  At this time, the data port of ADRV9002 is protected to be Hi-Z on the FPGA side.

#### **ADRV9002 Clock Selector (SW1)**
  You can select either the on-board TCXO or an external source (CN1) as the device clock source for the ADRV9002.
  
  If the clock is supplied from the on-board TCXO, the green LED (D1) lights up.



### **Cyclone V Connectors, Jumpers, Swithces**
#### **JTAG (CN8)**
| Pin No | Type | Mnemonic | Note |
----|----|----|----
| 1 | In | JTAG_TCK | Clock signal |
| 2 | Ground | GND | Signal ground |
| 3 | Out | HPS_TDO | Data from device |
| 4 | In | VCC3P3 | 3.3V power input |
| 5 | In | JTAG_TMS | JTAG state machine control |
| 6 | In | JTAG_RST | JTAG reset |
| 7 | Not Connect | NC | N/A |
| 8 | Not Connect | NC | N/A |
| 9 | In | JTAG_TDI | Data to device |
| 10 | Ground | GND | Signal ground |


#### **GPIO (CN9)**
| Pin No | Type | Mnemonic | Note |
----|----|----|----
| 1 | Ground | GND | Signal ground |
| 2 | Out | VCC3P3 | 3.3V power supply |
| 3 | Out | USR_OUT0 | USER_OUT[0] |
| 4 | Out | USR_OUT1 | USER_OUT[1] |
| 5 | Out | USR_OUT2 | USER_OUT[2] |
| 6 | Out | USR_OUT3 | USER_OUT[3] |
| 7 | Out | USR_OUT4 | USER_OUT[4] |
| 8 | Out | USR_OUT5 | USER_OUT[5] |
| 9 | Out | USR_OUT6 | USER_OUT[6] |
| 10 | Out | USR_OUT7 | USER_OUT[7] |
| 11 | Out | USR_OUT8 | USER_OUT[8] |
| 12 | Out | USR_OUT9 | USER_OUT[9] |
| 13 | Out | USR_OUT10 | USER_OUT[10] |
| 14 | Out | USR_OUT11 | USER_OUT[11] |
| 15 | Out | USR_OUT12 | USER_OUT[12] |
| 16 | Out | USR_OUT13 | USER_OUT[13] |
| 17 | Out | USR_OUT14 | USER_OUT[14] |
| 18 | Out | USR_OUT15 | USER_OUT[15] |
| 19 | Ground | GND | Signal ground |
| 20 | Out | VCC3P3 | 3.3V power supply |


#### **microSDXC Card Slot (CN10)**
  This board supports microSDXC card Up to **'To be confirmed'** GB.


#### **USB 2.0 (CN12)**
  USB OTG Connector, connected to USB1 of FPGA.

  USB Transceiver IC: Microchip USB3300
  
  USB Power Switch: OnSemi NCP380


#### **USB UART (CN13)**
  USB UART Connector, connected to UART0 of FPGA.

  USB to serial UART interface IC: FTDI FT232R

  Green LEDs (D11, D12) indicate that UART communication is in progress.
  For Tx (Yonaguni to PC), D12 lights up. For Rx (PC to Yonaguni), D11 lights up.


#### **Cyclone V Boot Selector (J3)**
  You can select the boot source from [QSPI/SD].


#### **Cyclone V MSEL (SW2)**
| bit | Signal name | Note |
----|----|----
| 1 | MSEL[4] | 0:ON, 1:OFF |
| 2 | MSEL[3] | 0:ON, 1:OFF |
| 3 | MSEL[2] | 0:ON, 1:OFF |
| 4 | MSEL[1] | 0:ON, 1:OFF |
| 5 | MSEL[0] | 0:ON, 1:OFF |
| 6 | N/A | |

  In Yonaguni, MSEL[4..0] is set to 00100.
  For further descriptions, refer Cyclone V Device Handbook.

  When FPGA fabric configuration is completed successfully, the green LED (D3) lights up.


#### **Reset Switch (SW3 - SW5)**
  | Switch No | Type | Mnemonic | Note |
----|----|----|----
| SW3 | Input | FPGA Reset | FPGA Hard Reset |
| SW4 | Input | HPS Reset | HPS Reset |
| SW5 | Input | HPS Warn Reset | HPS Warn Reset |


#### **User Defined DIP Switch (SW6)**
| bit | Signal name | Note |
----|----|----
| 1 | USER_DSW[3] | 0:ON, 1:OFF |
| 2 | USER_DSW[2] | 0:ON, 1:OFF |
| 3 | USER_DSW[1] | 0:ON, 1:OFF |
| 4 | USER_DSW[0] | 0:ON, 1:OFF |


#### **User Defined Button Switch (SW7, SW8)**
  | Switch No | Type | Mnemonic | Note |
----|----|----|----
| SW7 | Input | USER_BSW[1] | 0:ON, 1:OFF |
| SW7 | Input | USER_BSW[0] | 0:ON, 1:OFF |


#### **User Defined LED (D4 - D7)**
  Blue LEDs are connected to LED0-3. As default, all LEDs light up (USER_LED[3..0] is 0000).



### **ADAU1761 Connectors and Jumper**
#### **3.5 mm Audio Jack (J5, J6, J8, J9)**
| Jack No | Type | Mnemonic | Note |
----|----|----|----
| J5 | Out | HP_OUT | Headphone output |
| J6 | Out | LINE_IN | Line output |
| J8 | In | MIC_IN | Microphone input.  |
| J9 | In | LINE_IN | Line input |

  MIC BIAS (J7)

#### **ADAU1761 I2C Port (J13)**
To program ADAU1761, set SW9 to PGM.
| Pin No | Type | Mnemonic | Note |
----|----|----|----
| 1 | In | AC_DL_SCL | I2C SCL |
| 2 | Ground | GND | Ground |
| 3 | In/Out | AC_DL_SDA | I2C SDA |



### **Power Supply**
#### **USB Type-C (CN14)**
  In order to operate Yonaguni, a USB PD AC adapter capable of 9V/3A (≥27W) is required.
  When using USB PD as a power source, short J14.
  If supplying power directly, connect a 9V power supply to pin 2 of J14 and GND to pin 3 of J11.

  When power is supplied, the amber LED (D15) lights up.


#### **USB PD IC I2C Port (J10)**
| Pin No | Type | Mnemonic | Note |
----|----|----|----
| 1 | In | PD_SCL | I2C SCL |
| 2 | Ground | GND | Ground |
| 3 | In/Out | PD_SDA | I2C SDA |


#### **LTC2974/2977 PMBus Power Manager Interface (J11)**
| Pin No | Type | Mnemonic | Note |
----|----|----|----
| 1 | In | I3P3 | 3.3V Power input |
| 2 | In | PM_SCL | I2C SCL |
| 3 | Ground | GND | Ground |
| 4 | In/Out | PM_SDA | I2C SDA |


#### **LTC2974/2977 PMBus Power Manager Write Protect Pin (J12)**
If short J12, WP pin of LTC2974/2977 become low.

For more information about write protection function refer LTC2974/2977's datasheet.




## **Software Overview**




## **Repository information**
This repository contains BOM files, schematics, how to customize operating system.
```
README.md
Yonaguni_Rev2_BOM_r1.07.xlsx
Yonaguni_Schematic_r2.00Draft9.pdf
```

## **License**
MIT License

Copyright (c) 2023 Marimo Electronics Co., Ltd.

## **DISCLAIMER**
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
