# blueprint-tracker-i2c-scanner

A pre-built binary is a available in `tracker-i2cscanner.bin` or you can build your own from the source in the firmware directory.

An [I2C tutorial](https://docs.particle.io/hardware/expansion/about-i2c/) can be found in the Particle docs.

```cpp
#include "Particle.h"

SYSTEM_MODE(MANUAL);

void setup()
{
	Serial.begin(9600);

    waitFor(Serial.isConnected, 15000);
    delay(1000);

    pinMode(CAN_PWR, OUTPUT);
    digitalWrite(CAN_PWR, HIGH);
    delay(500);

	Wire3.begin();

	Serial.println("\nI2C Scanner");
}


void loop()
{
	byte error, address;
	int nDevices;

	Serial.println("Scanning...");

	nDevices = 0;
	for(address = 1; address < 127; address++ )
	{
		// The i2c_scanner uses the return value of
		// the Write.endTransmisstion to see if
		// a device did acknowledge to the address.
		Wire3.beginTransmission(address);
		error = Wire3.endTransmission();

		if (error == 0)
		{
			Serial.print("I2C device found at address 0x");
			if (address<16)
				Serial.print("0");
			Serial.print(address,HEX);
			Serial.println("  !");

			nDevices++;
		}
		else if (error==4)
		{
			Serial.print("Unknown error at address 0x");
			if (address<16)
				Serial.print("0");
			Serial.println(address,HEX);
		}
	}
	if (nDevices == 0)
		Serial.println("No I2C devices found\n");
	else
		Serial.println("done\n");

	delay(5000);           // wait 5 seconds for next scan
}

```

Of note:

- The Tracker One M8 connector uses `Wire3`. 
- You can use this firmware on other devices, but you will need to change this to `Wire`



