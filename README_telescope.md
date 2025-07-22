# For TP2m internal use only. Do not distribute outside the collaboration.

### File description
|Run|Filename|Description|Chinese|
|-|-|-|-|
|✓|**commands_cli.py**|Command query and Input format validation|望遠鏡指令查詢與輸入格式驗證|
|✓|fake_server.py|server simulator, run telescope_fake_server.py before run telescope_commands_cli.py|模擬伺服器，執行 telescope_commands_cli.py 前先執行 telescope_fake_server.py|
||commands_dict.py|dictionary format python code for telescope command|寫成 python 字典的望遠鏡指令|
||command_A_dict.py|dictionary format python code for command A response|寫成 python 字典的指令 A 回應|
|✓|**targets_add.py**|add targets to targets database|加入觀測目標到資料庫|
||targets_add.csv|targets add to database via batch import|用 csv 格式的檔案加入觀測目標資料庫|
||targets.db|targets database|目標資料庫|

<!-- ||command_controller.py|telescope command menu (API/class)|望遠鏡指令查詢（API/class）|
||mock_server.py|server simulator, run mock_server.py before run command_controller.py|模擬伺服器，執行 command_controller.py 前先執行 mock_server.py|
||command_definitions_structured_validation.py|dictionary format python code for telescope command, structured, with validation|寫成 python 字典的望遠鏡指令，格式有結構化，驗證指令參數|
||command_validation.py|validate command A response, not yet done|驗證指令 A 回應，未完成| -->

---
### 1. How to Query Command and Send Command to Telescope Server?

#### First step 
In terminal 1, type: 
```bash
$ python fake_server.py
```
#### Second step 
In terminal 2, type: 
```bash
$ python commands_cli.py
```
It will show:
```bash
Input command 'help' for available commands or 'exit' to quit: 
```
When you type 'help', a list of available commands, descriptions, and examples will be shown.
```
Available commands:
A: Get current information (Example: A 001 | A 001 003 008 121)
C: Send a dome stop command (Example: C)
D: Sending dome control commands (Example: D FFFF11110000)
...
```

**NOTE: ONLY the correct format of command will be sent to the server. ALL the format validation is done at local.**

- **Case 1-1: Command does not exist** (❌)  
If input command does not exist, it will return an error message.  
```
Input command or 'help' for available commands, 'exit' to quit: 
KK

Input error: Unknown command or wrong format: 'KK'
Please check the command and separate command and parameters with spaces.

```

- **Case 1-2: Command exists** (✅)  
If input command exists and no input parameter is required, it will return a response.
```
Input command or 'help' for available commands, 'exit' to quit: 
Y

Command Accepted: Y
Server Response : Y
```

- **Case 2-1: Parameter missing or wrong format** (❌)  
If input command exists but parameter is missing or with wrong format, it will return the format, example, and range.
```
Input command or 'help' for available commands, 'exit' to quit: 
T

Input error  : T requires 6 parameters
Input Format : T {ra} {dec} {ra_proper_motion} {dec_proper_motion} {epoch} {target_name}
Input Example: T 23:59:59.9 +89:59:59.9 0.0 0.0 2000.0 TARGET_NAME
Input Range  :
    ra : hour 0~23, minute 0~59, second 0.0~59.9
    dec : degree -89~89, minute 0~59, second 0.0~59.9
    ra_proper_motion : 0.0~9.9
    dec_proper_motion : 0.0~9.9
    epoch : 2000.0~2000.0
```

- **Case 2-2: Command exists with correct format** (✅)  
If input command exists with correct format, it will return a response.
```
Input command or 'help' for available commands, 'exit' to quit: 
M +359:59:59.9 0000.0 +359:59:59.9 0000.0 +359:59:59.9 0000.0

Command Accepted: M +359:59:59.9 0000.0 +359:59:59.9 0000.0 +359:59:59.9 0000.0
Server Response : OK
```

<a name="case-3-1"></a>
- **Case 3-1: Command A missing request number** (❌)  
If input command is `A`, you need to input at least one `request number`, or it will return all available `request number`.
```
Input command or 'help' for available commands, 'exit' to quit: 
A

Input error:   A requires at least 1 request_number (e.g. 001 002 ...)
Input Format:  A {request_number_1} {request_number_2} ...
Input Example: A 001 | A 001 003 008 121

Available request_numbers:
  001: Current year, month, and day
  002: Year, month, and day in UTC
  003: Modified Julian Day (MJD)
...
````
- **Case 3-2: Command A with correct format** (✅)  
When you input correct format, e.g., `A 001` or `A 001 007 371`, it will return the corresponding answer, `error code`, or `status code`.
```
Input command or 'help' for available commands, 'exit' to quit: 
A 001

Command Accepted: A 001 
Server Response : A 2025/07/07
```
or
```
Input command or 'help' for available commands, 'exit' to quit: 
A 007 016 371

Command Accepted: A 007 016 371
Server Response : A 12:34:56.7 165 0x00000001
```

---

### 2. How to Query the Meaning of Error/Status Code of Each Request Number in Command A

When you type `A {request_number} info`, it will return the detail information of the `{request_number}`.  
Available `{request_number}` can be found by typing `A` (see [Case 3-1](#case-3-1)).  
If the `error code` or `status code` does not exists, it will return no error/status code details.
```
Input command or 'help' for available commands, 'exit' to quit: 
A 001 info

Request number: 001
Description   : Current year, month, and day
Code type     : string
This request number does not have error/status code details.
```
If the `error code` or `status code` exists, it will return the description of the `error code` or `status code`.  
The error code will return a 3-digit number:
```
Input command or 'help' for available commands, 'exit' to quit: 
A 016 info

Request number: 016
Description   : Current error
Code type     : error_code
Error Codes:
  000: none
  001: Hardware failure
  002: Backup data error
  ...
```
Some `status code` will return numbers:
```
Input command or 'help' for available commands, 'exit' to quit: 
A 090 info

Request number: 090
Description   : Flag of observation ready state
Code type     : status_code
Status Codes:
  -1: Stopping
  0: Moving
  1: Tracking (ready for observation)
```
Some `status code` will return in `HEX` format:
```
Input command or 'help' for available commands, 'exit' to quit: 
A 370 info

Request number: 370
Description   : Extended Status
Code type     : status_code
Status Codes:
  0x0001: Maintenance is displayed
  0x0002: Parameter display
  0x0004: Monitor display
...
```
Some `status code` will return in `HEX` format and has 2 columns:
```
Input command or 'help' for available commands, 'exit' to quit: 
A 390 info

Request number: 390
Description   : Controller input state 3
Code type     : status_code
Status Codes:
  0x00000001: Reserved Bit 0-1               | Reserved Bit 0-2
  0x00000002: Reserved Bit 1-1               | Reserved Bit 1-2
  0x00000004: Reserved Bit 2-1               | Reserved Bit 2-2
```
Now you can understand the meaning `error code` or `status code`.

---

### 3. How to Add Targets to Observing Queue: (under development)
<!-- When you type 'observe_next', the telescope will start doing observation (under development).  -->

In terminal, type:
```
$ python targets_add.py
Database initialized.

Type 'manual' for manual input or 'file' to import from CSV file: 
manual

Enter target name: M81
Enter RA: 9:55:33
Enter Dec: 69:3:55
Enter exposure time for CCD155 (seconds): 30
Enter exposure time for CCD156 (seconds): 40
Enter exposure time for CCD157 (seconds): 50

=> Target added: M81
```
or
```
Type 'manual' for manual input or 'file' to import from CSV file:
file

Enter CSV filename:
targets_add.csv

=> Target added: M82
=> Target added: NGC253
=> Target added: NGC2146
=> Target added: NGC3628
```
The format of `targets_add.csv` is:
```
#Target Name,RA,Dec,Exposure_CCD155,Exposure_CCD156,Exposure_CCD157
M82,9 55 52,+69 40 47,30,60,90
NGC253,00h47m33.1s,-25d17m17.8s,10,20,30
NGC2146,06:18:37.7,+78:21:25.2,20,40,60
NGC3628,170.07,13.58,15,30,45
```
---
### Available Commands, Input Parameters, Input Format, and Response

43 available telescope commands are available, including 35 commands from Japanese version and 8 commands from English version that are not listed in Japanese version.  
  一共有 35 個日文版望遠鏡指令，以及 8 個日文版沒有英文版才有的望遠鏡指令，共 43 個指令。  

Japanese version command manual shows more details than English version..  
  日文版 對於指令描述以及格式說明比 英文版 詳細。

#### Command Reference

|ID| CMD | Description | Source | Params<sup>[1]</sup> | Parameter | Input | Format | Length Type | Length Spec | Range | Unit | Triangle Hint<sup>[2]</sup> | Triangle Bytes | Response | Note |
|:-|:--------|:------------|:-------|:-------|:----------|:------------|:----------|:------------|:------------|:--------|:--------|:--------------|:---------------|:--------|:---------|
|#1| A | Get current information | JP+EN | ≥1 | Request Number | 001 ~ 391 | 3-digit integer | fixed | 3 | 001 ~ 391 | - | - | - | A+[{Output}](#command-a-response-data) | Multiple input is allowed (e.g., `A 001` or `A 001 003 009`).<br> See Table [Command A Response Data](#command-a-response-data) for Request Number and Output Details. |
|#2| C | Send a dome stop command | JP | 0 | - | - | - | - | - | - | - | - | - | OK/NG | - |
|#3| D | Sending dome control commands | JP | 3 | Command | FFFF | 4-digit HEX | fixed | 4 | - | - | - | - | OK/NG | No space-separated between 3 parameters, (e.g., `D FFFF11110000`). |
|| | | | | Angle | 1111 | 4-digit HEX | fixed | 4 | - | - | t | 6~9 | | - |
|| | | | | Extension | 0000 | 4-digit HEX | fixed | 4 | - | - | t | 10~13 | | - |
|#4| E | Clear the error | JP+EN | 0 | - | - | - | - | - | - | - | $ | 1 | E | - |
|#5| F | Terminate the program | JP | 0 | - | - | - | - | - | - | - | $ | 1 | F | - |
|#6g| G | Mirror cover open | EN | 0 | - | - | - | - | - | - | - | - | - | G | - |
|#7g| H | Mirror cover close | EN | 0 | - | - | - | - | - | - | - | - | - | H | - |
|#8g| I | Clear secondary mirror scale A to zero | JP | 0 | - | - | - | - | - | - | - | $ | 1 | I | - |
|#10| K | Coordinate movement of the secondary mirror | JP+EN | 1 | Value | +/-00.001 | float | variable | 3~14 | -99.999 ~ +99.999 | mm | t | 3\~4, 6\~8 | OK/NG | - |
|#11| L | Returning the secondary mirror to its original position | JP+EN | 0 | - | - | - | - | - | - | - | $ | 1 | L | - |
|#12| M | Command movement in horizontal coordinate system | JP | 6 | Azimuth (Angle) | +/-359:59:59.9 | ±DDD:MM:SS.s | fixed | 12 | -359:59:59.9 ~ +359:59:59.9 | deg | t,? | 3\~5, 7\~8, 10\~11, 13 | OK/NG | - |
|| | | | | Azimuth (Speed) | 0.0 for 0000.0 | float | variable | 3\~6 | 0.0 ~ 0000.0 (?) | arcsec/s | t,? | 15~19, 21 | | automatic pad to 6 digits? |
|| | | | | Altitude (Angle) | +/-359:59:59.9 | ±DDD:MM:SS.s | fixed | 12 | -359:59:59.9 ~ +359:59:59.9 | deg | t,? | 24\~26, 28\~29, 31\~32, 34 | | - |
|| | | | | Altitude (Speed) | 0.0 for 0000.0 | float | variable | 3\~6 | 0.0 ~ 0000.0 (?) | arcsec/s | t,? | 36~40, 42 | | automatic pad to 6 digits? |
|| | | | | Rotator (Angle) | +/-359:59:59.9 | ±DDD:MM:SS.s | fixed | 12 | -359:59:59.9 ~ +359:59:59.9 | deg | t,? | 45\~47, 49\~50, 52\~53, 55 | | - |
|| | | | | Rotator (Speed) | 0.0 for 0000.0 | float | variable | 3\~6 | 0.0 ~ 0000.0 (?) | arcsec/s | t,$,? | 57~61, 63\~64 | | automatic pad to 6 digits? |
|#13| N | Sending an invalid command | JP | 0 | - | - | - | - | - | - | - | $ | 1 | N | - |
|#14| O | Power off the controller | JP+EN | 0 | - | - | - | - | - | - | - | $ | 1 | O | - |
|#15| P | Change the offset value | JP+EN | 6 | Offset RA | +/-3600.0 | float | variable | 3\~6 | -3600.0 ~ +3600.0 | arcsec | t | 5, 7 | OK/NG | - |
|| | | | | Offset DEC | +/-3600.0 | float | variable | 3\~6 | -3600.0 ~ +3600.0 | arcsec | t | 12, 14 | | - |
|| | | | | Offset Rotator | +/-180.0 | float | variable | 3\~6 | -180.0 ~ +180.0 | deg | t | 19, 21 | - | | - |
|| | | | | Offset Alt | 0.0 <br>*(must be 0.0)* | float | variable | 3\~6 | 0.0 | - | t | 26, 28 | | automatic pad to 6 digits? |
|| | | | | Offset Att | 0.0 <br>*(must be 0.0)* | float | variable | 3\~6 | 0.0 | - | t | 33, 35 | | automatic pad to 6 digits? |
|| | | | | Offset Time | 0.0 <br>*(must be 0.0)* | float | variable | 3\~6 | 0.0 | - | t,$ | 40, 42\~43 | | automatic pad to 6 digits? |
|#16| P0 | Clear the offset value | JP | 0 | - | - | - | - | - | - | - | t, $ | 1\~2 | OK/NG | - |
|#17| Q | Command an action once in the horizontal coordinate system | JP | 0 | - | - | - | - | - | - | - | - | - | OK/NG | - |
|#19| S | Stop | JP+EN | 0 | - | - | - | - | - | - | - | $ | 1 | S | - |
|#20| T | Move and Tracking in the equatorial coordinate system | JP+EN | 6 | RA | 23:59:59.9 | HH:MM:SS.s | fixed | 10 | 00:00:00.0 ~ 23:59:59.9 | sec | t | 2\~3, 5\~6, 8\~9, 11 | OK/NG | - |
|| | | | | DEC | +/-89:59:59.9 | ±DD:MM:SS.ss | fixed | 11 | -89:59:59.9 ~ +89:59:59.9 | deg | t | 14\~15, 18\~19, 21\~22, 24 | | - |
|| | | | | RA Proper Motion | 0.0 | float | fixed | 3 | 0.0 ~ 0.0 (?) | arcsec/s | t | 26, 28 | | - |
|| | | | | DEC Proper Motion | 0.0 | float | fixed | 3 | 0.0 ~ 0.0 (?) | arcsec/s | t | 30, 32 | | - |
|| | | | | Epoch | 2000.0 | float | fixed | 6 | 1900.0 ~ 2100.0 | year | t | 34~37, 39 | | - |
|| | | | | Target Name | `{TARGET_NAME}` | string | variable | 1\~20 | ≤20 chars | - | t, $, ? | 41\~47 | | green triangle only show up in 6 bytes |
|#21| U | The offset is added to the value to make it the new target. | JP | 0 | - | - | - | - | - | - | - | $ | 1 | OK/NG | - |
|#22g| X | Move for flat | EN | 0 | - | - | - | - | - | - | - | - | - | X | - |
|#23| Y | Move to the home position | JP+EN | 0 | - | - | - | - | - | - | - | $ | 1 | Y | - |
|#24g| Z | Telescope zero search | EN | 0 | - | - | - | - | - | - | - | $ | 1 | Z | - |
|#25| e | Search for the origin of the azimuth axis (X) | JP+EN | 0 | - | - | - | - | - | - | - | $ | 1 | e | - |
|#26| f | Search for altitude axis (Y) origin | JP+EN | 0 | - | - | - | - | - | - | - | $ | 1 | f | - |
|#27g| a | Nasmyth ON | EN | 0 | - | - | - | - | - | - | - | - | - | a | - |
|#28g| b | Nasmyth OFF | EN | 0 | - | - | - | - | - | - | - | - | - | b | - |
|#29g| c | Nasmyth Right | EN | 0 | - | - | - | - | - | - | - | - | - | c | - |
|#30g| d | Nasmyth Left | EN | 0 | - | - | - | - | - | - | - | - | - | d | - |
|#32| x | Dome linkage ON | JP | 0 | - | - | - | - | - | - | - | $ | 1 | OK/NG | - |
|#33| y | Dome linkage OFF | JP | 0 | - | - | - | - | - | - | - | $ | 1 | OK/NG | - |
|#34| D0 | Dome home position change | JP | 0 | - | - | - | - | - | - | - | t, $ | 1~2 | OK/NG | - |
|#36| DE | Dome emergency stop | JP | 0 | - | - | - | - | - | - | - | $ | 2 | OK/NG | - |
|#37| DM | Dome movement command | JP | 2 | Direction or Angle | `CW`\|`CCW`\|`RET`\|0\~3600 | string or integer | variable | 3~4 | 0000 ~ 3600 (3600 = 360.0 degs) | 0.1 deg | t | 3~6 | OK/NG | Input value can be either a string (`CW`, `CCW`, `RET`) or an integer (0\~3600, 3600 = 360.0 deg). |
|| | | | | Speed | `MAX`\|`HIGH`\|`MID`\|`LOW` | string | variable | 3~4 | - | - | - | - | | - |
|#38| DD | Dome position update | JP | 1 | Value | 3600 | integer | variable | 1~4 | 0000 ~ 3600 (3600 = 360.0 degs)| 0.1 deg | t,$ | 3~7 | OK/NG | automatic pad to 4 digits? |
|#39| DS | Dome slit opening and closing | JP | 1 | Action | `OPEN`\|`CLOSE`\|`STOP` | string | variable | 2~4 | - | - | $ for `DS CLOSE\r` | 8 | OK/NG | Only `DS CLOSE\r` has green triangle symbol at `\r` byte. |
|#40g| DL | Dome lighting ON/OFF | JP | 2 | State | `ON`\|`OFF` | string | variable | 2~3 | - | - | - | - | OK/NG | - |
|| | | | | Brightness | 000 ~ 100 | integer | variable | 1~3 | 000 ~ 100 | % | t | 6~8 \| 7~9 | | Green triangle symbol shows at bytes 6\~8th for `DL ON 100`, but 7\~9th for `DL OFF 000`. |
|#45| j | Move to secondary mirror position command value | JP | 1 | Value | +/-00.100 | float | variable | 3~14 | -99.999 ~ +99.999 | mm | t,? | 3\~4, 6\~8 | OK/NG | Green triangle symbol shows at 3\~4th, 6\~8th, the bytes end at 14th, but byte 9\~13th are missing. |
|#46| k | Change secondary mirror position (change A value) | JP | 1 | Value | +/-00.100 | float | variable | 3~7 | -99.999 ~ +99.999 | mm | t,$ | 3\~4, 6\~9 | OK/NG | - |
|#47| ks | Change secondary mirror position (change B value) | JP | 1 | Value | +/-00.100 | float | variable | 3~7 | -99.999 ~ +99.999 | mm | t,$ | 3\~4, 6\~9 | OK/NG | - |
|#52| s | Tracking operation based on satellite orbit info (TLE) | JP | 1 | TLE Data | *[TLE format](https://en.wikipedia.org/wiki/Two-line_element_set)* | string | fixed | 166 | - | - | t, $ | 1, 26\~166 | OK/NG | For TLE format, see [Two-Line Element set (Wikipedia)](https://en.wikipedia.org/wiki/Two-line_element_set).   |
|#53| sf | Satellite file (TLE format) read\|monitor\|off operation | JP | 1 | State | `{TLE.dat}`\|`\r`\|`off`| string | variable | 14,2,6 | - | - | $,? | 2 for `sf\r` | OK/NG | Only `sf\r` shows green triangle symbol at `\r`. <br>`sf off` does not show any green triangle. <br>`sf {TLE.dat}` does not show green triangle symbol but end at bytes 14th.   |
|#54| ss | Satellite tracking based on schedule file | JP | 1 | File Name | `{schedule.sch}` | string | variable | 15 | - | - | ? | - | OK/NG | No green triangle symbol shows, but bytes end at 15th. |
|#55| sw | Schedule file monitoring ON/OFF | JP | 1 | State | `on`\|`off` | string | variable | 2~3 | - | - | t,$,? | 3~5 for `sw on\r` | OK/NG | Only `sw on\r` shows green triangle symbol at `on` and `\r`, `sw off\r` does not show any. |
| **Total** | **43** |-| **JP(35)<br>EN(8)** | **≥36** | **-** | **-** | **-** | **-** | **-** | **-** | **-** | **-** | **-** | **-** | **-** |

**Notes:**  
**[1] Params Interpretation:**
- For params = 0, no input value is needed after type command (e.g., `E`). 
- For params = 1, an input is needed after type command (e.g., `K -00.001` or `DS CLOSE`). 
- For params > 1, multiple inputs are needed after command, separated by space (e.g., `DM RET LOW` or `DM 3600 HIGH`). 
 
**[2] Triangle Hint Interpretation:** 
- `t `: Indicates the byte cell has a green triangle symbol, typically marking fixed-length or structured fields. 
- `$ `: Denotes the **`\r`** or **`CR` (Carriage Return)** byte also shows a triangle, meaning CR carries structural significance. 
- `- `: No triangle marking, possibly a flexible or unstructured field. 
- `? `: Confusing format or inconsistent marking; needs further clarification.  
<!-- 
**[3] Command A Input:** 1 or multiple inputs allowed, space-separated (e.g., `A 001` or `A 001 003 009`).  
**[4] Command A Output:** See Table [Request Number](#command-a-response-data) for Output Details.  
**[5] Command D:** Although 3 parameters are required, these HEX values are combined into a **single 12-character string without spaces** when sent (e.g., `D FFFF11110000`).  
**[6] Command DM Direction:** Input value can be either a string (`CW`, `CCW`, `RET`) or an integer (0\~3600, 3600 = 360.0 deg).  
**[7] Command DS:** Only `DS CLOSE\r` has green triangle symbol at `CR` byte.  
**[8] Command DL Brightness:** Green triangle symbol shows at bytes 6\~8th for `DL ON 100`, but 7\~9th for `DL OFF 000`.  
**[9] Command j:** Green triangle symbol shows at 3\~4th, 6\~8th, the bytes end at 14th, but byte 9\~13th are missing.  
**[10] Command s:** For TLE format, see [Two-Line Element set (Wikipedia)](https://en.wikipedia.org/wiki/Two-line_element_set).  
**[11] Command sf:** 3 options are `sf {TLE.dat}`, `sf`, `sf off`. `{TLE.dat}` is a TLE format file, `sf {TLE.dat}` means load the `{TLE.dat}` file. `sf` means execute `{TLE.dat}` file. `sf off` means stop execute `{TLE.dat}` file.  Only `sf\r` shows green triangle symbol at `\r`. `sf off` does not show any green triangle. `sf {TLE.dat}` does not show green triangle symbol but end at bytes 14th.  
**[12] Command ss:** No green triangle symbol shows, but bytes end at 15th.  
**[13] Command sw:** Only `sw on\r` shows green triangle symbol at `on` and `\r`, `sw off\r` does not show any. -->

These hints are based on visual symbols in the original command manual and assist in identifying structured regions within command packets.


Among the 43 available commands, command `A` has 54 available `request number` (`request number` 001 ~ 391), 
allowing you to check the current telescope status.  
43 個望遠鏡指令中，Command A 有 54 種望遠鏡狀態 (查詢碼 001 ~ 391) 可供查詢。

Among the 54 available `request number`, 14 will return either an `error code` or a `status code`.  
54 個可供查詢的望遠鏡狀態查詢碼中，有 14 個會根據查詢的問題回報屬於何種錯誤或何種狀態。

An `error code` will be returned for an error type requests. A `status code` will be returned for a status request.  
詢問何種錯誤會回報 `error code`，詢問何種狀態會回報 `status code`。

The number of available returned codes for these 14 `request number` is listed as follows:  
望遠鏡針對這 14 個查詢碼的回應，分別有以下幾種不同的回傳碼可供查詢：

#### Command A Response Data

|Request number|Description|Output|Format|Range|Unit|Length Type|Length Spec|Code Count|Code Type|
|:--:|:--|:--|:--|:--|:--|:--|:--|:--:|:--:|
|001|Current year, month, and day|2025/07/07|YYYY/MM/DD|-|day|fixed|10|-|-|
|002|Year, month, and day in UTC|2025/07/07|YYYY/MM/DD|-|day|fixed|10|-|-|
|003|Modified Julian Day (MJD)|60869.7|float|-|day|variable|3~10|-|-|
|004|Current time|86399.9|float|0.0 ~ 86399.9|sec|variable|3~10|-|-|
|005|Current time (Text string display)|12:34:56.7|HH:MM:SS.s|00:00:00.0 ~ 23:59:59.9|sec|fixed|10|-|-|
|006|Universal Time|86399.9|float|0.0 ~ 86399.9|sec|variable|3~10|-|-|
|007|Universal Time (Text string display)|12:34:56.7|HH:MM:SS.s|00:00:00.0 ~ 23:59:59.9|sec|fixed|10|-|-|
|008|UT1 - UTC|0.1|float|-|sec|fixed|3|-|
|009|Current local sidereal time|86399.9|float|0.0 ~ 86399.9|sec|variable|3~10|-|-|
|010|Current azimuth angle|±1296000.0|float|-1296000.0 ~ +1296000.0|arcsec|variable|3~10|-|-|
|011|Current azimuth angle (°)|±360.0|float|-360.0 ~ +360.0|deg|variable|3~6|-|-|
|012|Current altitude angle|324000.0|float|-0.0 ~ 324000.0|deg|variable|3~10|-|-|
|013|Current altitude angle (°)|0.0 ~ 92.0|float|0.0 ~ 92.0|deg|variable|3~4|-|-|
|015|Rotator posture angle (°)|±360.0|float|-360.0 ~ +360.0|deg|variable|3~6|-|-|
|016|Current error|000 ~ 165|3-digit integer|000 ~ 165|-|fixed|3|44|error code|
|017|Current status|0x0001|4-digit HEX|-|-|fixed|4|16|status code|
|018|Current right ascension|86399.999|float|0.0 ~ 86399.999|sec|variable|3~9|-|-|
|019|Current right ascension (string)|12:34:56.789|HH:MM:SS.sss|00:00:00.000 ~ 23:59:59.999|sec|fixed|12|-|-|
|020|Current declination|±32400.0|float|-324000.00 ~ +324000.00|sec|variable|3~10|-|-|
|021|Current declination (string)|±12:34:56.78|±DD:MM:SS.ss|-89:59:59.99 ~ +89:59:59.99|arcsec|fixed|12|-|-|
|025|Focus position A|(?)|float|(?)|mm|variable|-|-|-|
|026|Focus position B|(?)|float|(?)|mm|variable|-|-|-|
|027|Focus position A-B|(?)|float|(?)|mm|variable|-|-|-|
|033|Speed command (position)|±10800.0|float|-10800.0 ~ +10800.0|arcsec/s|variable|3~8|-|-|
|035|Speed command (high)|±10800.0|float|-10800.0 ~ +10800.0|arcsec/s|variable|3~8|-|-|
|050|Offset 1 (RA)|±3600.0|float|-3600.0 ~ +3600.0|arcsec|variable|3~7|-|-|
|051|Offset 1 (DEC)|±3600.0|float|-3600.0 ~ +3600.0|arcsec|variable|3~7|-|-|
|052|Offset 1 (position)|±3600.0|float|-3600.0 ~ +3600.0|arcsec|variable|3~7|-|-|
|053|Offset 1 (altitude)|±3600.0|float|-3600.0 ~ +3600.0|arcsec|variable|3~7|-|-|
|054|Offset 1 (attitude/rotator)|±270.0|float|-270.0 ~ +270.0|deg|variable|3~6|-|-|
|078|Offset 1 (time)|±10.0|float|-10.0 ~ +10.0|sec|variable|3~5|-|-|
|090|Observation ready flag|-1, 0, 1|integer|-1 ~ 1|-|variable|1~2|3|status code|
|120|Dome angle (×10)|0 ~ 3600|integer|0 ~ 3600 (3600 = 360.0 degrees)|0.1 deg|variable|1~4|-|-|
|121|Dome status|0x00000001|8-digit HEX|-|-|fixed|8|28|status code|
|370|Extended status|0x0001|4-digit HEX|-|-|fixed|4|16|status code|
|371|Controller input 1|0x00000001|8-digit HEX|-|-|fixed|8|32|status code|
|372|Controller input 2|0x00000001|8-digit HEX|-|-|fixed|8|32|status code|
|373|Controller output 1|0x00000001|8-digit HEX|-|-|fixed|8|32|status code|
|374|Controller output 2|0x00000001|8-digit HEX|-|-|fixed|8|32|status code|
|385|Axis sensor status (azimuth)|0x0001|4-digit HEX|-|-|fixed|4|13|status code|
|386|Axis sensor status (altitude)|0x0001|4-digit HEX|-|-|fixed|4|13|status code|
|389|Axis sensor status (secondary mirror)|0x0001|4-digit HEX|-|-|fixed|4|13|status code|
|390|Controller input 3|0x00000001|8-digit HEX|-|-|fixed|8|32|status code|
|391|Controller output 3|0x00000001|8-digit HEX|-|-|fixed|8|32|status code|
| **Total** | **54** | - | - | - |- | - | - | **338** |**14** |

**Notes:**
- **Output**: Example of the return value.
- **Format**: Specifies the data format, e.g., `YYYY/MM/DD`, `float`, `HEX`.
- **Length Type**: Indicates if the length is `fixed` or `variable`.
- **Code Count**: Number of possible codes for error or status responses. A dash (-) indicates no specific code count.
- **Code Type**: Identifies if the response is an `error code` or `status code`. This field is left blank for other data types.

---

