# Check_iDRAC version 2.1
Nguyen Duc Trung Dung (ndtdung@spsvietnam.vn - dung.nguyendt@gmail.com)

website: dybn.blogspot.com

NOC leader/R&D/Application specialist at SwissPostSolution VietNam

#Manual 

## How it works?
- Simple, this check use snmpwalk command which included with net-snmp package you installed.
- It run snmpwalk instance to send SNMP request to idrac and parsing result data into human readable form.

## Requires:
- This check included with DELL IDRAC MIB file (required). You can copy MIB file to your default MIB folder, usually
is /usr/share/snmp/mibs/. If you don't want to do this just keep it where you want and user option -m/--mib with path to
it.

## Supported hardware type:
- Power Supply
- Power Unit
- Fan
- Memory
- Battery CMOS
- Physical Disk
- Virtual Disk
- CPU
- Sensor

## How to use?
### Everything in cli
**Scan all**

./check_idrac_2.py -H 10.10.10.20 -v2c -c public

```PS
PS
--PS 1: OK, Volt I/O: 264 V/230 V, Current: 0.4 A, Watt I/O: 900 W/750 W
--PS 2: OK, Volt I/O: 264 V/230 V, Current: 0.2 A, Watt I/O: 900 W/750 W
FAN
--System Board Fan1: 2760 RPM - ENABLED/OK
--System Board Fan2: 2760 RPM - ENABLED/OK
--System Board Fan3: 2880 RPM - ENABLED/OK
--System Board Fan4: 2520 RPM - ENABLED/OK
--System Board Fan5: 2760 RPM - ENABLED/OK
--System Board Fan6: 2760 RPM - ENABLED/OK
BATTERY
--System Board CMOS Battery: ENABLED/OK [PRESENCEDETECTED]
--PERC1 ROMB Battery: ENABLED/OK [PRESENCEDETECTED]
--PERC2 ROMB Battery: ENABLED/OK [0]
PU
--PU 1: ENABLED/OK, RedundancyStatus: FULL, SystemBoard Pwr Consumption: 112 W
MEM
--Memory 1 (DIMM Socket A1) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC73]
--Memory 2 (DIMM Socket A2) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC8D]
--Memory 3 (DIMM Socket B1) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC8A]
--Memory 4 (DIMM Socket B2) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC09]
VDISK
--VDisk 1 (XXX): OK/ONLINE, RAID-5 (1675.12 GB), BadBlock: 0 [Virtual Disk 0 on Integrated RAID Controller 1]
DISK
--PDisk 1 (0:1:0) 558.38 GB: READY, PowerStat: SPUNUP, HotSpare: dedicated [WD, S/N: WXA1E63UYD10]
--PDisk 2 (0:1:1) 558.38 GB: ONLINE, PowerStat: SPUNUP, HotSpare: no [WD, S/N: WXA1E63UYC84]
--PDisk 3 (0:1:2) 558.38 GB: ONLINE, PowerStat: SPUNUP, HotSpare: no [WD, S/N: WXA1E63UXM21]
--PDisk 4 (0:1:3) 558.38 GB: ONLINE, PowerStat: SPUNUP, HotSpare: no [WD, S/N: WXA1E63UXX39]
--PDisk 5 (0:1:4) 558.38 GB: ONLINE, PowerStat: SPUNUP, HotSpare: no [WD, S/N: WXA1E63UXS14]
SENSOR
--System Board Inlet Temp: 22.0 C ENABLED/OK
--System Board Exhaust Temp: 30.0 C ENABLED/OK
--CPU1 Temp: 40.0 C ENABLED/OK
--CPU2 Temp: 41.0 C ENABLED/OK
CPU
--CPU 1 (8 cores/16 threads): ENABLED/OK [Intel(R) Xeon(R) CPU E5-2650 0 @ 2.00GHz]
--CPU 2 (8 cores/16 threads): ENABLED/OK [Intel(R) Xeon(R) CPU E5-2650 0 @ 2.00GHz]
```

**Group scan**

./check_idrac_2.py -H 10.10.10.20 -v2c -c public -w MEM

```
Memory 1 (DIMM Socket A1) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC73]
Memory 2 (DIMM Socket A2) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC8D]
Memory 3 (DIMM Socket B1) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC8A]
Memory 4 (DIMM Socket B2) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC09]
```

**Check specific hardware**

./idrac2.1.py -H 10.10.10.20 -v2c -c public -w MEM#3

```
OK - Memory 3 (DIMM Socket B1) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC8A]
```

***Everything in configuration file and for specific hardware***

./check_idrac_2.py -H 10.10.10.20 -f check_idrac.conf -w MEM#2

***Set alert threshold on Output WATT for Power Supply***

./idrac2.1.py -H 10.10.10.20 -v2c -c public -w PS#1 --wat-warn=none,750

```
WARN - PS 1: OK, Volt I/O: 264 V/0 V, Current: 0.4 A, Watt I/O: 900.0 W/750(!) W
```

***Set alert for Fan#1***

./idrac2.1.py -H 10.10.10.20 -v2c -c public -w FAN#1 --fan-warn=4000,6000 --fan-crit=3000,7000

***Set alert for Fan#1 in configuration file***

./idrac2.1.py -H 10.10.10.20 -v2c -c public -w FAN#1

within configuration file, under [fan] section:

```
[fan]
thesholds = 4000,6000,3000,7000
```

or 

```
[fan]
thesholds = none,6000,3000,7000
```

./idrac2.1.py -H 10.10.10.20 -v2c -c public -w FAN#1 --fan-warn=4000

this is the same as above example

## What is STATE ALERT DEFINITION?
- Every admin has their own reason/style when checking device so I dont want to force you to expected
an alert like I do. That is reason for State alert and is defined in config file, that means
every hardware part which has status as: ok/online/ready/disable/enable... will have
alert rule based on these configs.
- As in sample config file you will see something like this:
OK = ok|online|spunup|full|ready|enabled|presence
WARN = $ALL$
CRIT = critical|nonRecoverable|fail

- "OK" (config) means, in example with third RAM DIMM:
OK - Memory 3 (DIMM Socket B1) 16.00 GB/1600 MHz: ENABLED/OK [DDR3, Samsung, S/N: 36BDCC8A]
- You see the words: ENABLED/OK ? The prefix "OK" exist because we defined "OK = enabled|ok" in config. If you change
config to OK = online, so the output will be:

```
WARN - Memory 3 (DIMM Socket B1) 16.00 GB/1600 MHz: ENABLED(!)/OK(!) [DDR3, Samsung, S/N: 36BDCC8A]
```

Its because "WARN = $ALL$" so all status the out of "OK" and "CRIT" range will be WARN.
- You can either define state alert by options --ok/--warn/--crit or in config file.
- You can not use $ALL$ for two states at once.
- I use simple search, in ex "online" will matchs onlines/xxonline/onlinebutnotok... but since device does not return
those weird outputs so we will be ok.
- If no $ALL$ in state definition, all states that are out of states defined here will be reported as UNKNOWN.

Why would someone need --no-alert?
- For Testing purpose, check will not ask for threshold and speed up analyzing processes.
- No alert also disables performance data output.

- "-n" option will bypass alert function (also not ask for threshold) and always return with exit code = 0. Be carefull
do not use "-n/--no-alert" options in production environment!!!
