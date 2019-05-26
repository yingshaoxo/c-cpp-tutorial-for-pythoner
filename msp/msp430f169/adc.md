# Analog to Digital Converter

## Warning: this article is not completed at this moment!



```c
#include <msp430.h>

int main(void)
{
    WDTCTL = WDTPW + WDTHOLD;                 // Stop WDT
    ADC12CTL0 = SHT0_2 + ADC12ON;            // Set sampling time, turn on ADC12
    ADC12CTL1 = SHP;                          // Use sampling timer
    ADC12IE = 0x01;                           // Enable interrupt
    ADC12CTL0 |= ENC;                         // Conversion enabled
    P6SEL |= 0x01;                            // P6.0 ADC option select
    P1DIR |= 0x01;                            // P1.0 output

    for (;;)
    {
        ADC12CTL0 |= ADC12SC;                   // Sampling open
        __bis_SR_register(CPUOFF + GIE);      // LPM0, ADC12_ISR will force exit
    }
}

// ADC12 interrupt service routine
#if defined(__TI_COMPILER_VERSION__) || defined(__IAR_SYSTEMS_ICC__)
#pragma vector=ADC12_VECTOR
__interrupt void ADC12_ISR(void)
#elif defined(__GNUC__)
void __attribute__ ((interrupt(ADC12_VECTOR))) ADC12_ISR (void)
#else
#error Compiler not supported!
#endif
{
    if (ADC12MEM0 < 0x7FF)
        P1OUT &= ~0x01;                       // Clear P1.0 LED off
    else
        P1OUT |= 0x01;                        // Set P1.0 LED on
    __bic_SR_register_on_exit(CPUOFF);      // Clear CPUOFF bit from 0(SR)
}
```

ADC12 = \(12-bit sample-and-hold\) **Analog-to-Digital Converter**

> An ADC's job is to perform a conversion by sampling `an analog voltage` into `a digital value`.

ADC12CTL0 = ADC12 Control Register 0

> The ADC12 conversion core is configured using `ADC12CTL0` and `ADC12CTL1`.
>
> A `Control Register` is nothing but a variable where stores `controlling information`.

SHT0\_2 = Sample Hold 0, Select Bit: 2



## References:

{% embed url="https://users.wpi.edu/~ndemarinis/ece2049/e16/lecture10.html" %}







