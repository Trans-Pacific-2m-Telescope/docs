## CCD Manual and command


###  📘  Command List and Note
| Function Code | Operation | function | command package | return package  | Done?  |
| --- | --- | --- | --- | --- | --- |
| 1011 | Get Camera Status | Y | struct.pack(">IBBHH", 10, 128, 1, 1011,0) | Ack,  |  |
| 1012 | Image Acquisition | Y | struct.pack(">IBBHH+IHHH+lenS", 10+10+len, 128, 1, 1012,10+len, exptime,Mode,Buffer, as,f) |  |  |
| 1013 | Dark Acquisition | Y | struct.pack(">IBBHH+IHHH+lenS", 10+10+len, 128, 1, 1013,10+len, exptime,Mode,Buffer, as,f) |  |  |
| 1014 | Test Pattern Acquisition | Y | struct.pack(">IBBHH+IHHH+lenS", 10+10+len, 128, 1, 1014,10+len, exptime,Mode,Buffer, as,f) |  |  |
| 1016 | Triggered Acquisition | Y |  |  |  |
| 1017 | Inquire Acquisition Status | Y | struct.pack(">IBBHH", 10, 128, 1, 1017,0) |  |  |
| 1018 | Terminate Acquisition | Y | struct.pack(">IBBHH", 10, 128, 1, 1018,0) |  |  |
| 1019 | Retrieve Image | Y/S | struct.pack(">IBBHHH", 10+2, 128, 0, 1019,2,Buffer) |  |  |
| 1024 | Get Image Header | Y/s | struct.pack(">IBBHHH", 10+2, 128, 0, 1024,2,Buffer) |  |  |
| 1028 | Average Light Images Acquisition |  |  |  |  |
| 1029 | Average Dark Images Acquisition |  |  |  |  |
| 1030 | Average Triggered Images Acquisition |  |  |  |  |
| 1031 | Save Image | Y/S | struct.pack(">IBBHH+HH+lenS", 10+4+len, 128, 0, 1031,4+len,Buffer,as,f) |  |  |
| 1032 | TDI Acquisition, Internally Paced |  |  |  |  |
| 1033 | TDI Acquisition, Externally Paced |  |  |  |  |
| 1034 | Set Acquisition Mode | Y | struct.pack(">IBBHHB", 10+1, 128, 1, 1034,1,AcqMode) |  |  |
| 1035 | Set Exposure Time | Y | struct.pack(">IBBHHI", 10+4, 128, 1, 1035,4,exptime) |  |  |
| 1036 | Set Acquisition Type | Y | struct.pack(">IBBHHHB", 10+3, 128, 1, 1036,3,Buffer,AcqType) |  |  |
| 1037 | Acquire | Y | struct.pack(">IBBHH+HHH+lenS", 10+6+len, 128, 1, 1012,6+len ,Mode,Buffer, as,f) |  |  |
| 1038 | Set Number of Averages |  |  |  |  |
| 1039 | Set Number of Frames |  |  |  |  |
| 1040 | Set Multiple Frame Buffer Mode |  |  |  |  |
| 1041 | Get SI Image Settings | Y/S | struct.pack(">IBBHH", 10, 128, 0, 1041, 0) |  |  |
| 1042 | Set Readout Mode | Y | struct.pack(">IBBHHB", 10+1, 128, 1, 1042,1,ROutNum) |  |  |
| 1043 | Set CCD Format Parameters |  |  |  |  |
| 1044 | Set Single Readout & Format Parameter |  |  |  |  |
| 1045 | Set Single Configuration Parameter |  |  |  |  |
| 1046 | Cooler On/Off | Y | struct.pack(">IBBHHB", 11, 128, 1, 1046,1, coolon) |  |  |
| 1047 | Set Save to Folder Path |  |  |  |  |
| 1048 | Get Camera Parameters | Y/S | struct.pack(">IBBHH", 10, 128, 0, 1048, 0) |  |  |
| 1049 | Expose & Shift N Lines | /S |  |  |  |
| 1070 | Exchange Image and Cache | Y | struct.pack(">IBBHH", 10, 128, 1, 1070, 0) |  |  |
| 1071 | Set Background Image |  |  |  |  |
| 1072 | Subtract Background |  |  |  |  |



## ✳️ Abbreviations & Parameters Explanation
- In the **"function"** column:  
  - **Y** indicates that this function **needs to be implemented** and should be included in the code. If it is not marked with **Y**, then we **do not require** this function for our application.  
  - **/S** indicates that this is a **server command**, not a CCD command.
 
- **Mode:** data  mode: default in this project is 4
  - `1`: transmit to client
  - `2`: hold in buffer.
  - `3`: Save file and transmit to client
  - `4`: Save file and hold in buffer.

- **Buffer**: buffer 1 or 2,  default in this project is 1
- **as:** Save file type as:  default in this project is 1
  - `0`: Fits U16
  - `1`: Fits I16
  - `2`: Fits I32
  - `3`: Fits SGL
  - `4`: TIFF U16
  - `5`: TIFF I16
  - `6`: TIFF I32
  - `7`: TIFF SGL
- **f:** filenname of saving (end with null,b"\x00")
- **len:** lengh of f(filename) note: if len=5 lenS means 5S.

- **AcqMode** (Acquistion Mode):  default is 0
  - '0': single Image
  - '1': avarage
  - '2': Multiple 
  - '3': Multiple Frames
  - '4': focus

- **AcqType** (Acquistion Types):  default is 0
  - `0`: Light
  - `1`: Dark
  - `2`: Test
  - `3`: Triggered
  - `4`: TDI Internally Paced
  - `5`: TDI Externally Paced

- **ROutNum** (Readout Mode numbere):  shoule between (0~9)



