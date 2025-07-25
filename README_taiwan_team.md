
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
        - Current progress: Command query and a simulator for sending commands.
        - Developer: An-Li Tsai
  
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
- Update content (2025/07/22)
