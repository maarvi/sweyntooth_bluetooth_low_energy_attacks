## SweyTooth - Unleashing Mayhem over Bluetooth Low Energy
**This repository is part of a research outcome from the [ASSET Research Group](https://asset-group.github.io/disclosures/sweyntooth/).**
![attack_logo](sweintooth_logo.svg)

SweynTooth captures a family of **12 vulnerabilities** (more under non-disclosure) across different Bluetooth Low Energy (BLE) software development kits (SDKs) of six major system-on-a-chip (SoC) vendors. The vulnerabilities expose flaws in specific BLE SoC implementations that allow an attacker in radio range to trigger **deadlocks, crashes** and **buffer overflows** or **completely bypass** **security** depending on the circumstances.

You can check more information about the vulnerabilities, **available patches** and affected devices on [ASSET Research Group SweynTooth disclosure website](https://asset-group.github.io/disclosures/sweintooth/).


### Getting Started

SweynTooth uses the [Nordic nRF52840 Dongle](https://www.nordicsemi.com/?sc_itemid={CDCCA013-FE4C-4655-B20C-1557AB6568C9}) to send raw link layer packets to and from the vulnerable peripheral. It is necessary to flash the driver firmware to the board before starting the Python 2.7 pr.

The binary of our firmware code is on the `nRF52_driver_firmware.zip` file. You need to install nrfutil tool to flash the firmware on the board. Remember to put the nRF52840 on DFU mode before flashing (reset the USB dongle while it is connected to your PC by pressing the reset button). You can run the following commands to install the Python dependencies and to flash the firmware:

```shell
python -m pip install nrfutil pyserial
nrfutil dfu usb-serial -p COM_PORT -pkg nRF52_driver_firmware.zip
```

The scripts work on Linux or Windows. You just need to change the `COM_PORT` parameter to match the nRF52840 port name. You can alternatively flash the firmware by using the [nRF Connect App for Destop](https://www.nordicsemi.com/Software-and-tools/Development-Tools/nRF-Connect-for-desktop), which gives a nice interface to flash the firmware.

### Running the proof of concept scripts

After the requirements are installed, you can run an exploit script by executing the following command:

```shell
python Telink_key_size_overflow.py COM7 A4:C1:38:D8:AD:A9
```

The first argument is the serial port name (generally /dev/ttyACM0 on Linux) and the second is the address of the vulnerable BLE device. You can use any BLE scanner or the nRF Connect App to discover such address. 

Taking as example the Key Size Overflow vulnerability,  the following output is given by the script if the vulnerable device hangs after the crash:

![attack_logo](demo_output.png)



#### Available BLE exploits

Each exploit script corresponds to one flaw. The following summary table captures the correspondence between the vulnerability and a script to exploit the vulnerability on the affected SoCs.

| Vulnerability              | CVE(s)                                                       | Vendor             | Script file                        |
| -------------------------- | ------------------------------------------------------------ | :----------------- | :--------------------------------- |
| Link Layer Length Overflow | [CVE-2019-16336](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-16336)<br />[CVE-2019-17519](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17519) | Cypress<br />NXP   | link_layer_length_overflow.py      |
| LLID Deadlock              | [CVE-2019-17061](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17061)<br />[CVE-2019-17060](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17060) | Cypress<br />NXP   | llid_dealock.py                    |
| Truncated L2CAP            | [CVE-2019-17517](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17517) | Dialog             | DA14580_exploit_att_crash.py       |
| Silent Length Overflow     | [CVE-2019-17518](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17518) | Dialog             | DA14680_exploit_silent_overflow.py |
| Public Key Crash           | [CVE-2019-17520](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17520) | Texas Instruments  | CC2640R2_public_key_crash.py       |
| Invalid Connection Request | [CVE-2019-19193](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19193) | Texas Instruments  | CC_connection_req_crash.py         |
| Invalid L2CAP Fragment     | [CVE-2019-19195](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19195) | Microchip          | Microchip_invalid_lcap_fragment.py |
| Sequential ATT Deadlock    | [CVE-2019-19192](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19192) | STMicroelectronics | sequential_att_deadlock.py         |
| Key Size Overflow          | [CVE-2019-19196](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19196) | Telink             | Telink_key_size_overflow.py        |
| Zero LTK Installation      | [CVE-2019-19194](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-19194) | Telink             | **TBA**                            |

Generally, products using the affected SoCs employ a watchdog to automatically restart the BLE SoC in the case a fault occurs, hence not all products can be deadlocked. Nevertheless, it should be possible to get some visual or audio indication from the product if the same crashes and restarts.



### Captures

The folder **captures** contains some sample captures of each vulnerability. We have also added some noncompliance cases detected in some SoCs.



### Acknowledgements
**This research was partially supported by [Keysight Technologies](https://www.keysight.com/sg/en/home.html).**