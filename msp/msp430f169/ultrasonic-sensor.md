# Ultrasonic Sensor

## Basic Knowledge

### Time Units

* 1 millisencond = $$\frac{1\ second}{1000}$$ 
* 1 microsecond = $$\frac{1\ millisecond}{1000}$$ 

### Clock Cycle

**Clock Cycle** is the speed of a computer processor, or CPU, is determined by the clock cycle, which is the amount of time between two pulses of an oscillator. Generally speaking, the higher number of pulses per second, the faster the computer processor will be able to process information. The clock speed is measured in Hz, typically either megahertz \(MHz\) or gigahertz \(GHz\). For example, a 4GHz processor performs 4,000,000,000 clock cycles per second.

1 MHz = 1,000,000 Hz = 1000000 clock cycle per second

For example, if you have a computer which runs at 1 MHz, then, for 1 ms\(millisecond\), it will clock 1000 times \(or go through 1000 clock cycles\).

## Codes

```c
#include <msp430.h> 

// Definition for the ultrasonic sensor
#define trigger_pin BIT3
#define echo_pin BIT4

#define set_trigger_pin_as_output P1DIR |= trigger_pin
#define set_echo_pin_as_input P1DIR &= ~echo_pin

#define set_trigger_pin_to_0 P1OUT &= ~trigger_pin
#define set_trigger_pin_to_1 P1OUT |= trigger_pin

#define input_from_echo_pin (P1IN & echo_pin)


void millisecond_level_of_delay(unsigned int t)
{
    while (t--)
    {
        // delay for 1ms
        __delay_cycles(1000);
    }
}

void microsecond_level_of_delay(unsigned int t)
{
    while (t--)
    {
        // delay for 1/1000 ms
        __delay_cycles(1);
    }
}

unsigned long int microseconds_to_clock_cycles(unsigned long int number) {
	return number;
}

unsigned long int clock_cycles_to_microseconds(unsigned long int number) {
	return number;
}

int initialize_ultrasonic_sensor() {
	set_trigger_pin_as_output;
	set_echo_pin_as_input;
}

int pulseIn(int state, unsigned long timeout) {
	// state is 0 or 1

	unsigned long pluse_width = 0;

	// for time control
	// convert the timeout from microseconds to a number of times through
	// the initial loop; it takes 16 clock cycles per iteration.
	unsigned long number_of_loops = 0;
	unsigned long max_loops = microseconds_to_clock_cycles(timeout)/16;

	// wait for any previous pulse to end
	while (input_from_echo_pin == state)
		if (number_of_loops++ == max_loops)
			return 0;
	
	// wait for the pulse to start
	while (input_from_echo_pin != state)
		if (number_of_loops++ == max_loops)
			return 0;
	
	// wait for the pulse to stop
	while (input_from_echo_pin == state) {
		if (number_of_loops++ == max_loops)
			return 0;

		pluse_width++;
	}

	// convert the reading to microseconds. The loop has been determined
	// to be 20 clock cycles long and have about 16 clocks between the edge
	// and the start of the loop. There will be some error introduced by
	// the interrupt handlers.
	return clock_cycles_to_microseconds(pluse_width * 21 + 16);
}

int ultrasonic_detection() {
	set_trigger_pin_to_0;
	microsecond_level_of_delay(2);
	set_trigger_pin_to_1;
	microsecond_level_of_delay(20);
	set_trigger_pin_to_0;

    unsigned long int duration = pulseIn(HIGH, 0);

    duration = duration / 59;
    if ((duration < 2) || (duration > 300)) {
        return 0;
    }

    return duration;
}

int main(void)
{
	WDTCTL = WDTPW | WDTHOLD;	// stop watchdog timer
	
	return 0;
}
```

### pluseIn

`pluseIn(pluse input)` is a function which reads a pluse. For example, if `value` is `HIGH`, `pulseIn()` waits for the pin to go from `LOW` to `HIGH`, starts timing, then waits for the pin to go `LOW` and stops timing. Returns the length of the pulse in microseconds.

Or simply, `pluseIn(LOW)` will return the length of how long the signal is LOW.

## References:

{% embed url="https://github.com/yingshaoxo/driver/blob/dbafd5f936facfd53a55293209c93da9aa5f8ca5/experience/old.ino\#L289" %}

{% embed url="https://www.arduino.cc/reference/en/language/functions/advanced-io/pulsein/" %}

{% embed url="https://forum.arduino.cc/index.php?topic=105289.0" %}

{% embed url="https://stackoverflow.com/questions/50346546/how-to-measure-frequency-in-arduino-using-pulsein-function" %}

{% embed url="https://stackoverflow.com/questions/43651954/what-is-a-clock-cycle-and-clock-speed" %}



