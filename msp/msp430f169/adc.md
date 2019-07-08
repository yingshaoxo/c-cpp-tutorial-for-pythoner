# Analog to Digital Converter

## A simple example

```c
//  Description: A single sample is made on A0 with reference to AVcc.
//  Software sets ADC10SC to start sample and conversion - ADC12SC
//  automatically cleared at EOC. ADC12 internal oscillator times sample (16x)
//  and conversion. In Mainloop MSP430 waits in LPM0 to save power until ADC12
//  conversion complete, ADC12_ISR will force exit from LPM0 in Mainloop on
//  reti. If A0 > 0.5*AVcc, P1.0 set, else reset.
//
//                MSP430F149
//             -----------------
//         /|\|              XIN|-
//          | |                 |
//          --|RST          XOUT|-
//            |                 |
//      Vin-->|P6.0/A0      P1.0|--> LED
//
//  M. Buccini
//  Texas Instruments Inc.
//  Feb 2005
//  Built with CCE Version: 3.2.0 and IAR Embedded Workbench Version: 3.21A
//******************************************************************************

#include <msp430.h>

int main(void)
{
    WDTCTL = WDTPW + WDTHOLD;                 // Stop WDT

    ADC12CTL0 = SHT0_2 + ADC12ON;            // Set Sample/Hold time ;  Turn On ADC12
    ADC12CTL1 = SHP;                          // Use sampling timer | ADC12 Sample/Hold Pulse Mode
    ADC12IE = 0x01;                           // Enable interrupt | ADC12 Interrupt Enable
    ADC12CTL0 |= ENC;                         // Conversion enabled | ADC12 Enable Conversion
    P6SEL |= 0x01;                            // P6.0 ADC option select | Port 6 Selection; set this pin as  Peripheral-pin, not just simple I/O Function anymore
    P1DIR |= 0x01;                            // P1.0 output

    for (;;)
    {
        // This should actually happen in a timer interrupt where
        // we may like to sample only once in, say 1 second
        ADC12CTL0 |= ADC12SC;                   // Sampling open | ADC12 Start Conversion
        __bis_SR_register(CPUOFF + GIE);      // LPM0, ADC12_ISR will force exit
    }
}

// ADC12 interrupt service routine
#pragma vector=ADC12_VECTOR
__interrupt void ADC12_ISR(void)
{
    if (ADC12MEM0 < 0x7FF)
        P1OUT &= ~0x01;                       // Clear P1.0 LED off
    else
        P1OUT |= 0x01;                        // Set P1.0 LED on

    __bic_SR_register_on_exit(CPUOFF);      // Clear CPUOFF bit from 0(SR)
}
```

### About ADC12

ADC12 = \(12-bit sample-and-hold\) **Analog-to-Digital Converter**

> An ADC's job is to perform a conversion by sampling `an analog voltage` into `a digital value`.

ADC12CTL0 = ADC12 Control Register 0

> The ADC12 conversion core is configured using `ADC12CTL0` and `ADC12CTL1`.
>
> A `Control Register` is nothing but a variable where stores `controlling information`.

SHT0\_2 = Sample Hold 0, Select Bit: 2

ADC12MEM0 = ADC12 Conversion Memory 0

> Digital result will be saving to `ADC12MEM(ADC12 Conversion Memory)`

### About PxSEL

P6SEL = Port 6 Select Register

> Each PxSEL bit is used to select the pin function − `I/O port` or `peripheral module` function.

* P6SEL = 0:  `I/O Function` is selected for the pin
* P6SEL = 1:  `Peripheral module function` is selected for the pin

### SR\_register

`__bis_SR_register(LPM0_bits)` : Enter Low Power Mode 0

`__bic_SR_register_on_exit(LPM0_bits)` : Exit Low Power Mode 0

## References:

{% embed url="https://users.wpi.edu/~ndemarinis/ece2049/e16/lecture10.html" %}

{% embed url="https://www.egr.msu.edu/classes/ece480/capstone/spring13/group04/application/Application%20note-karl.pdf" %}







