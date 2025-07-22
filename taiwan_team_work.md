# Project Overview & Status Updates
## The Trans-Pacific Two-Meter (TP2m) Telescope

The upcoming (2027) **Trans-Pacific Two-Meter Telescope (TP2m)** is poised to become a critical tool for **time-domain astronomy**, focusing on the observation of **transients** and **Targets of Opportunity (ToOs)**. Located at San Pedro Mártir Observatory in Mexico, it operates about three hours behind the Vera C. Rubin Observatory's LSST and eight hours ahead of **Taiwan’s Lulin One-Meter Telescope (LOT)**, forming a collaborative, **multi-time-zone network** to monitor transient events. When **LSST** detects and identifies potential targets, TP2m and LOT provide timely follow-up observations. By confirming and characterizing transient phenomena such as **supernovae, gamma-ray bursts, and kilonovae**, TP2m will make significant contributions to the global study of dynamic celestial phenomena. 
![TP2m map](./img/tp2m_map.png)
(Image credit: NCUIA)

TP2m team has six partners:
- **NCUIA (Institute of Astronomy, National Central University, Taiwan) 中央大學天文所**
- ASIAA (Institute of Astronomy & Astrophysics, Academia Sinica, Taiwan) 中研院天文與天文物理研究所
- SHAO (SHanghai Astronomical Observatory, China) 上海天文台
- HNAS (HeNan Academy of Sciences, China) 河南省科學院
- **UNAM (Universidad Nacional Autónoma de México, Mexico) 墨西哥自治大學**
- AMU (Aix-Marseille Université, France) 法國艾克斯-馬賽大學

## TP2m Telescope Control System (TCS) software

The current concept of the TP2m Control System (TCS) software is shown in the following schematic diagram.

![TP2m Schematic diagram](./img/TP2m_schematic_diagram_20250703.png)
(Image credit: An-Li Tsai)

The TCS includes several modules.
- **Telescope & Dome control (NCUIA's telescope)**
- **CCD camera control (NCUIA's instrument)**
- IR camera control (NCUIA's instrument)
- Spectrometer control (UNAM's instrument)
- Weather monitoring system
- Scheduler
- Alert system (ex. transient/ToO events…)
- Data processor
- Data storage/transfer/mirror site
- Monitoring on UPS, power... 
- Logs
- Firewall
- Proposal submission/review
- **UI for observers**
- **UI for engineers**
- UI for proposal applicants/reviewers 

## TCS Software - Taiwan team
Taiwan team (NCUIA and ASIAA) is working on hardware communication, specifically for：
1. Multi-color CCD Camera 
2. Telescope communication 
3. UI for Observers and Engineers

### 1. The Multi-color CCD Camera control
#### Hardware Info
- Current location: ASIAA B1, Taipai, Taiwan  
  ![Multi-color CCD Cameras group photo](./img/P_20250227_154821.jpg)
  (Image credit: An-Li Tsai)
- Manufacture: Spectral Instrument (SI) Inc.
- Number of CCD Cameras: 3  
  ![Multi-color CCD Cameras](./img/P_20250310_165826.jpg)
  (Image credit: An-Li Tsai)
- CCD Type: SI-1100
- CCD size: 2048 x 4096
- Filter: ccd155: r’ ; ccd156: y ; ccd157: z
- CCD Type: E2V 44-82 NIMO BI DD Astronomy process ER1 coating
- Cooler Type: PT-30 cryotiger  

#### Software Info
- Server Software
    - OS: Windows PC (each CCD is controlled by one PC)
    ![CCD Camera control computers](./img/P_20250310_170055.jpg)
    (Image credit: An-Li Tsai)
    - Software: *"SI Image SGL Rev.F"*
    ![SI Image SGL](./img/2mT-camera_py_5300.png)
    (Image credit: An-Li Tsai)
    - Manual: *"SI Image SGL Rev.F"*

- Client Software
    - OS: Linux
    ![CCD Camera linux computers](./img/P_20250321_154531.jpg)
    (Image credit: An-Li Tsai)
    - Status: In development.
    - Developers: Sheng-Feng Yen, Wei-Hung Liao, and An-Li Tsai


### 2. The Telescope & Dome control
#### Hardware Info
- Current location: Nishimura Mfg., Shiga, Japan
- Manufacture: Nishimura Telescope Mfg.
    ![Nishimura telescope](./img/PXL_20230531_064132563.PANO_-2.jpg)
    (Image credit: Nishimura Mfg.)
- Prime Mirror Clear Aperture: 2,000mm
- Prime Mirror Focus Length: 4,000mm
- Optical System  FOV at prime Focus: 1.0゜
- Second Mirror Clear Aperture: 609mm
- Mechanism System Type: Alt-Azimuth mount Friction Drive
- Pointing Accuracy: 2 arcsec RMS/Altitude: over 30-88 degrees
- Drive Speed: maximum slew rate 4 degrees/sec Azimuth and 2 degrees/sec Altitude axes
- Zenith Blind Spot: 2 degrees of Radius
- Rotation angles: 
    - Azimuth:±270; 
    - Altitude: 10-95; 
    - Altitude: 
        - From 10 to 88 degrees; (control software limit) ; 
        - From 9.5 to 90 degrees; (electric limit) ;
        - From 9-91 degrees; (mechanism limit)
#### Software Info
- Server Software
    - OS: Linux
 - Client Software
    - Linux Program: 
        - Status: In development.
        - Developers: An-Li Tsai and Sheng-Feng Yen

### 3. Control UI
- UI mode: 
    - UI for Observers
    - UI for Engineers
- OS: Linux
- Interface: Terminal-based UI
    ![TCS UI](./img/sfyen_tp2m_20250623_p5.png)
    (Image credit: Sheng-Feng Yen)
- Status: In development
- Developer: Sheng-Feng Yen

---

### Article Update Log
- README.md created by An-Li Tsai (2025/07/03).
- Update images (2025/07/04).
