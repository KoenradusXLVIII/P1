# P1 API module
This module can be used to read P1 telegrams from a serial interface. I purchased a pre-build
P1 FTDI cable after several DIY experiments failed. I purchased [this one](https://nl.aliexpress.com/item/FTDI-USB-Kabel-voor-P1-Poort-Nederlandse-Slimme-Meter-Kamstrup-162-382-EN351-Landis-Gyr-E350/32945225256.html)
, but a quick internet search should yield several. The P1 API module automatically reads a 
full telegram from the serial interface, checks to make sure there are no CRC errors and
then computes and stores *current net power consumption* and *total net energy consumption*.

## Usage example
Simply import the package and initiate a client by providing the port name the FTDI cable
is connected to. Then read a single telegram by calling the read_telegram functions. Power and
energy data can then by obtained by calling the associated get functions. 

```python
import P1

p1_client = P1.Client('/dev/ttyUSB0')
p1_client.read_telegram()

print('Current net power consumption : %.2f [kW]' % p1_client.get_power())
print('Total net energy consumption  : %.2f [kWh]' % p1_client.get_energy())
```
### Net power and energy consumption calculation
Net power consumption is computed by substracting current power export (1-0:2.7.0) from
current power import (1-0:1.7.0). Total net energy consumption is computed by substracting 
total net energy export (1-0:2.8.1 (low) and 1-0:2.8.2 (high)) from total net energy import
(1-0:1.8.1 (low) and 1-0:1.8.2 (high)).

### Reference telegram
This reference telegram would yield the following values:
- Power: 6.00 kW
- Energy: 44.32 kW
```
/XMX5LGBBFG10

1-3:0.2.8(42)
0-0:1.0.0(181225103211W)
0-0:96.1.1(4530303331303033313034393833373135)
1-0:1.8.1(002640.860*kWh)
1-0:1.8.2(001164.335*kWh)
1-0:2.8.1(001076.832*kWh)
1-0:2.8.2(002684.048*kWh)
0-0:96.14.0(0001)
1-0:1.7.0(00.006*kW)
1-0:2.7.0(00.000*kW)
0-0:96.7.21(00008)
0-0:96.7.9(00003)
1-0:99.97.0(3)(0-0:96.7.19)(170214183908W)(0000006810*s)(161018133550S)(0000005875*s)(160604162916S)(0000011017*s)
1-0:32.32.0(00000)
1-0:32.36.0(00000)
0-0:96.13.1()
0-0:96.13.0()
1-0:31.7.0(002*A)
1-0:21.7.0(00.006*kW)
1-0:22.7.0(00.000*kW)
!2FE6
```