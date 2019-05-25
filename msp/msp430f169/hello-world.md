# Hello World

## Light up your first LED

```text
#include <msp430.h>

int main(void)
{
    volatile unsigned int i;

    WDTCTL = WDTPW + WDTHOLD;                   // Stop WDT
    P1DIR |= BIT0;                            // P1.0 set as output

    while (1)                                  // continuous loop
    {
        P1OUT ^= BIT0;                          // XOR P1.0
        for (i = 50000; i > 0; i--) {
            ;
        }                   // Delay
    }
}
```

WD = Watch dog

> A watchdog default behavior is to reset the micro-controller unless the firmware periodically lets it know everything is running fine. This is known as "feeding the dog" or "kicking the dog". This way, if your firmware gets stuck in a loop or otherwise stops operating as expected, the watchdog is not fed and will reset the chip.

WDT = Watch Dog Timer

WDTCTL = Watchdog Timer Control

WDTPW = Watchdog Timer Pass Word

> Since it is the duty of WDT to protect against software glitches, WDT itself is protected by an internal password. If we tried to read or write to the watch dog control register \(WDTCTL\) without the password, the micro-controller will get reset. The password is defined in the header file as WDTPW.

WDTHOLD = Watchdog Timer Hold \(on\)

> Preventing watchdog timer to reset, just hold on.

BIT0 = Binary Digit 0

## References:

{% embed url="https://electronics.stackexchange.com/questions/120984/why-do-programs-stop-watchdog-timer-on-msp430" %}

{% embed url="https://www.xanthium.in/watch-dog-timer-in-msp430g2xxx" %}







