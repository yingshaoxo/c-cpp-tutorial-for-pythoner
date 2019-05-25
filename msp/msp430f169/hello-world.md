# Hello World

## Light up your first LED

```text
#include <msp430.h>

int main(void)
{
    volatile unsigned int i;

    WDTCTL = WDTPW + WDTHOLD;                   // Stop WDT
    P6DIR |= BIT0;                              // P6.0 set as output, BIT0=0x0001

    while (1)
    {
        P6OUT ^= BIT0;                          // XOR P6.0, reverse its value
        for (i = 50000; i > 0; i--) {
            ;
        }                                       // Delay
    }
}
```

## Watchdog

WD = Watch dog

> A watchdog default behavior is to reset the micro-controller unless the firmware periodically lets it know everything is running fine. This is known as "feeding the dog" or "kicking the dog". This way, if your firmware gets stuck in a loop or otherwise stops operating as expected, the watchdog is not fed and will reset the chip.

WDT = Watch Dog Timer

WDTCTL = Watchdog Timer Control

WDTPW = Watchdog Timer Pass Word

> Since it is the duty of WDT to protect against software glitches, WDT itself is protected by an internal password. If we tried to read or write to the watch dog control register \(WDTCTL\) without the password, the micro-controller will get reset. The password is defined in the header file as WDTPW.

WDTHOLD = Watchdog Timer Hold \(on\)

> Preventing watchdog timer to reset, just hold on.

## Port

P6DIR = Port 6 Direction

> For a pin, it only has two direction, input or output.

Set a `pin 6.2` as an `output pin`: `P6DIR |= BIT2`

* BIT0 = Binary Digit 0 = x.0
* BIT1 = Binary Digit 1 = x.1
* BIT2 = Binary Digit 2 = x.2

## Wire your LED

For this type of micro-controller, you have to connect `LED pin` with `Pin 60`by yourself.

## References:

{% embed url="https://electronics.stackexchange.com/questions/120984/why-do-programs-stop-watchdog-timer-on-msp430" %}

{% embed url="https://www.xanthium.in/watch-dog-timer-in-msp430g2xxx" %}







