# Objectives
- Design a PCB that includes as much of topics of interest as possible
- Design a device that can work as a macro mini keyboard with a knob (maybe a few keys). Inspired by a weather knob and the elgato streaming board
- Dunno

# Specifics
1. Motor:
		[Small Gimbal Motor](https://a.co/d/aC6gKuL) that goes on top of the pcb 28mm Diam, 15mm height 
2. Control:
	- Use an FOC control system with a micro controller. This would allow to make cool interactions with the motor. 
	- This woudl require a 3 phase contol IC, Mosfets, Shunt Resistors, bulk capacitors, maybe opamps for current sensing. 
	-  A magnetic encoder is also required for precise control. It needs to go under where the motor would go. MT6701
	- Some type of position encoder could also be useful. Maybe a hall sensor and a magnet on the carcass of the motor could be a simple yet effective tool.
3. Power
	- The pcb needs a battery to power the motor (7.4V). 2S cpuld work and be compatc, maybe 3S is more reliable but needs more stuff.
	- A small/simple BMS woudl control the battery to prevent damage or high discharge
	- A smart charging circuit is also necessary
	- When available, Thunderbolt should be able to move the motor (Risky but cool) 
	- When connected, the system shoudl use the battery to move the motor, but use the usb to power logic and charge th ebattery slowly
	- It should be able to be wireless for a decent amount of time with light use (8 hours)
4. Communication
	- Usbc port for usb device. (a jumper to toggle programming? or is it better to have separate programming pins/port)
	- bluetooth device - Esp32?
5. User Interface
	- interactive knob with different modes (click when turning, joystick, force feedback)
	- Circular screen on top of the motor (If infinite turning, how to connect the screen with a small slip ring). Hopefully touch for better interaction. If the motor has hollow shaft but too slim, no way to support the screen. Slim aluinum tube as support maybe. 
	- Maaaaaaaaaybe a few mechanical keys
	