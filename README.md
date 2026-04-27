# Bertec LabVIEW Slip/Trip App
Application to deliver controlled accelerations/decelerations at specified times during the gait cycle using a standard split-belt treadmill.

## Included:
- AG_PertApp.VI - LabVIEW VI of the perturbation application
  
## Setup:
1. Install Bertec Treadmill Control Panel (v1.7.30)
2. Install LabVIEW (tested on LabVIEW 17.0 32-bit)
3. Configure NI USB-6211 DAQ. App currently expects Left vGRF signal in Dev1/ai14 and Right vGRF signal in Dev1/ai15. Ensure "device name" on top right of VI Front Panel corresponds to NI USB-6211 device number. 
4. Turn on Bertec Treadmill
5. Open Bertec Treadmill Control Panel and enable TCP Remote Connection. Settings --> Enable Remote Control --> Enable TCP --> Listen on Port 4000 --> Enable Listen on 127.0.0.1 Only. Click Enable Remote Control
6. Change perturbation parameters if desired
7. Run VI
8. Zero forceplate using "Left Zero" and "Right Zero" buttons on the right-side of Front Panel

### How it works:
<img width="770" height="607" alt="AG_FlowChart_v3" src="https://github.com/user-attachments/assets/9764893e-bb9e-4404-9930-4dfff5e6933a" />

Vertical ground reaction forces generated during walking on the split-belt treadmill are acquired in real time and streamed to a data acquisition board. These signals are used to detect gait events and trigger perturbation logic, which is packaged into command data and sent to the treadmill control software. The treadmill software then issues velocity and acceleration commands to apply the desired perturbation during walking.  Analog gait signals are used to detect heel-strike (HS) and toe-off (TO) events, from which average stance duration is calculated using the previous five stance durations. User-defined perturbation parameters, including perturbation condition (left/right slip or trip) and onset delay relative to the detected gait event, are used to generate a command data packet. This packet is transmitted via a TCP connection to the treadmill control computer to initiate the desired perturbation. 

## Front Panel Walkthrough:

### Perturbation Setting (Radio Buttons)
- Predetermined: Allow to set perturbation type on a set step count. Must be filled prior to start of running the VI. Found on the top left-side of the Block Diagram
- Random: Create random order of perturbations that occur every 20-35 steps. All perturbations will occur 10 times (10 x 4 = 40 total perturbations).
- Manual: Manually select perturbation button (Left/Right Slip/Trip)

### Belt Speed (Slider)
- Set treadmill to desired belt velocity (m/s)

### Reset Steps Counter (Button)
- Reset step counter to allow for accurate step count to start once belt is at desired speed.

### Speed Parameter (Radio Button)
- Quickly change perturbation parameters (Slip Speed, Trip Speed, etc) for 0.7 m/s settings or 1.0 m/s settings

### Perturbation (Buttons)
- Buttons used to set which perturbation occur. Note: Pressing a perturbation buttons will not initiate the perturbation. It will prime the app to initiate the perturbation upon the next heelstrike detected

### Developer Mode (Buttons)
- Enables testing perturbation buttons without needing a vGRF signal. Press perturbation button and then the corresponding developer perturbation button to emulate a heel strike

### Perturbation Parameters (Numeric Controls)
- Numeric controls that determine the timing and intensity of the perturbations

### Treadmill Control (Button)
- Changes belt acceleration/deceleration to 0.2 m/s^2, allows for smooth and slow increase to belt speed. If changing speeds using the "Belt Speed" slider, this button must be enabled. SEE KNOWN CHALLENGES SECTION FOR MORE DETAILS

### Treadmill Stop (Button)
- Sets belt-velocity to 0.0 m/s at 0.2 m/s^2. Use this button to stop the treadmill without needing to use the "Belt Speed" slider.

### Left/Right Zero (Button)
- Independent software zeros for the left and right belt

### Device Name (I/O Dropdown)
- Device number of the NI USB-6211 DAQ. Default is set to Dev1

### IP Address (String Field)
- IP Address to connect to Bertec Treadmill Control Panel. Default is 127.0.0.1

### Remote Port (Numeric Control)
- Remote Port value to connect to Bertec Treadmill Control Panel. Default is 4000

## Known challenges (READ ME):

### Coding
-  ***Be sure "Treadmill Control START" button is enabled after app starts. By default, the Bertec treadmill will accelerate/decelerate to the target belt speed at 10 m/s^2 when the belt speed slider changes and the "Treadmill Control START" button is not enabled. Enabling the "Treadmill Control START" button changes the belt acceleration/deceleration to 0.2 m/s^2. Once the belt is at speed, turn off "Treadmill Control START" button to allow for perturbations to function with proper accelerations. If changing steady state belt speed, "Treadmill Control START" must be enabled. Likewise, pressing "Treadmill STOP" button will decelerate the belt to 0 m/s at 0.2 m/s^2.

### Operation
- We have found that ~15% of perturbations result in accelerations/decelerations lasting approximately double the desired duration (default is 0.2 seconds). Conversation with Bertec Support suggest running the LabVIEW VI and treadmill control panel at a high priority (task manager) and closing out of all other applications may reduce incidental prolonged perturbations. 
