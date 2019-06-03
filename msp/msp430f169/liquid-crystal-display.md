# Liquid-crystal display

## All in all

> LCD: Liquid-crystal display

Here we gonna use `ST7920` \(a LCD which has 128x64 pixels\)

If you use `Arduino` for development, then you should use [u8glib](https://github.com/olikraus/u8glib) to finish this mission as quickly as possible.

## SPI Interface

SPI = Serial Peripheral Interface

{% embed url="https://www.analog.com/en/analog-dialogue/articles/introduction-to-spi-interface.html" %}

To be honest, I don't really know those things.

* Clock \(SPI CLK, SCLK\)
* Chip select \(CS\)
* Master out, slave in \(MOSI\)
* Master in, slave out \(MISO\)

SPI is a synchronous, full duplex master-slave-based interface.

The device that generates the clock signal is called the master. Data transmitted between the master and the slave is synchronized to the clock generated by the master.

SPI interfaces can have only one master and can have one or multiple slaves.

The chip select signal from the master is used to select the slave.

MOSI and MISO are the data lines. MOSI transmits data from the master to the slave and MISO transmits data from the slave to the master.

## ST7920 LCD Pin-Map

<table>
  <thead>
    <tr>
      <th style="text-align:left">Pin Name</th>
      <th style="text-align:left"><b>Description</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">GND</td>
      <td style="text-align:left">Ground</td>
    </tr>
    <tr>
      <td style="text-align:left">VCC</td>
      <td style="text-align:left">Input supply voltage (2.7v to 5.5v, mostly 3.3v)</td>
    </tr>
    <tr>
      <td style="text-align:left">V0</td>
      <td style="text-align:left">LCD bias voltage, for contrast setting</td>
    </tr>
    <tr>
      <td style="text-align:left">RS (CS*)</td>
      <td style="text-align:left">
        <p><b>Parallel Mode</b>: Register Select (write Command or write Data)</p>
        <p>0: Select instruction register (write) or busy flag, address counter (read)</p>
        <p>1: Select data register (write/read).</p>
        <p>(0 for instruction writing, 1 for data writing)</p>
        <p><b>Serial mode</b>: Chip Select</p>
        <p>1: chip enabled</p>
        <p>0: chip disabled.</p>
        <p>(When chip is disabled, SID and SCLK should be set as &#x201C;H&#x201D;
          or &#x201C;L&#x201D;. Transcient of SID and SCLK is not allowed.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">R/W (SID*)</td>
      <td style="text-align:left">
        <p><b>Parallel Mode</b>: Read/Write control; 0 for write, 1 for read.</p>
        <p><b>Serial Mode</b>: Serial Input Data. (<code>Serial Data Input</code> would
          be better)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">E (SCLK*)</td>
      <td style="text-align:left">
        <p><b>Parallel Mode</b>: Set 1 to Enable LCD, set 0 to disable LCD.</p>
        <p><b>Serial Mode</b>: Serial Clock. (may connected to a source clock)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">DB0 - DB7</td>
      <td style="text-align:left">Data Pins (used in <b>parallel mode</b> , 8/4bit)</td>
    </tr>
    <tr>
      <td style="text-align:left">PSB</td>
      <td style="text-align:left">
        <p>Interface selection:</p>
        <p><b>Serial Mode</b>: 0</p>
        <p><b>Parallel Mode</b>: 1</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">NC</td>
      <td style="text-align:left">Not connected (Test pins), useless.</td>
    </tr>
    <tr>
      <td style="text-align:left">RST</td>
      <td style="text-align:left">Reset Pin</td>
    </tr>
    <tr>
      <td style="text-align:left">VOUT</td>
      <td style="text-align:left">LCD voltage doubler output. VOUT &#x2266; 7V.</td>
    </tr>
    <tr>
      <td style="text-align:left">BLA</td>
      <td style="text-align:left">Backlight(Background brightness) positive power supply. (3.3V or 5V)</td>
    </tr>
    <tr>
      <td style="text-align:left">BLK</td>
      <td style="text-align:left">Backlight(Background brightness) Negative power supply. (0V or Ground)</td>
    </tr>
  </tbody>
</table>## What's the difference between Parallel Mode and Serial Mode?

At High-level programming language, `Parallel Mode` will have better transfer performance.

### Parallel Mode:

You send data through multiple wire at same time.

At here, I mean use `ST7920 LCD`, you have to connect `DB0 - DB7` to your `MSP430 board` .

### Serial Mode:

You send data just through one wire. Data will be sent one by one with time passing.

At here, I mean use `ST7920 LCD`, you will use Pin `CS(RS), SID(R/W), SCLK(E), PSB`.

## Complaint

Chinese are so \*\*! \(What should I say? stupid or selfishness?\)

Almost every LCD chip they have created is copied from other foreign countries.

But they never say a word about its copyright or where it came from. You can't even know the original chip name.

For example, `ST7920 LCD`, in China `Taobao` store, you can only search it by typing `12864LCD`. They even treat `12864LCD` like it's a type of LCD. But we both know it's nothing but screen resolution.

## Codes

```c
#include  "msp430.h"

#define CS1 P1OUT |= BIT0
#define CS0 P1OUT &= BIT0
#define SID1 P1OUT |= BIT1
#define SID0 P1OUT &= ~BIT1
#define SCLK1 P1OUT |= BIT2
#define SCLK0 P1OUT &= ~BIT2

void DelayUs2x(unsigned char t)
{
    while (--t)
        ;
}

void delay_1ms(unsigned char t)
{

    while (t--)
    {
        //大致延时1mS
        DelayUs2x(245);
        DelayUs2x(245);
    }
}

void sendbyte(unsigned char zdata)
{
    unsigned int i;

    for (i = 0; i < 8; i++)
    {
        if ((zdata << i) & 0x80)
        {
            SID1;
        }
        else
        {
            SID0;
        }
        SCLK0;
        SCLK1;
    }
}

void write_com(unsigned char cmdcode)
{
    CS1;
    sendbyte(0xf8);   //1 1 1 1 RS RW 0   写操作RW=0 11111000写数据 11111010写指令
    sendbyte(cmdcode & 0xf0);
    sendbyte((cmdcode << 4) & 0xf0);
    delay_1ms(1);
    CS0;
}

void write_data(unsigned char Dispdata)
{
    CS1;
    sendbyte(0xfa);
    sendbyte(Dispdata & 0xf0);
    sendbyte((Dispdata << 4) & 0xf0);
    delay_1ms(1);
    CS0;
}

void Put_String(unsigned int x, unsigned int y, unsigned char* s)
{
    switch (y)
    {
    case 1:
        write_com(0x80 + x);
        break;
    case 2:
        write_com(0x90 + x);
        break;
    case 3:
        write_com(0x88 + x);
        break;
    case 4:
        write_com(0x98 + x);
        break;
    default:
        break;
    }
    while (*s > 0)
    {
        write_data(*s);
        s++;
        DelayUs2x(50);
    }
}

void lcdinit()
{
    delay_1ms(200);
    write_com(0x30);  // 功能设定：基本指令集
    delay_1ms(20);
    write_com(0x0c);  // 显示状态：整体显示，游标关
    delay_1ms(20);
    write_com(0x01);  // clean display
    delay_1ms(200);
}

int main(void)
{
    WDTCTL = WDTPW + WDTHOLD; // close watchdog
    P1DIR = 0xFF;
    P1OUT = 0x00;
    
    __delay_cycles(1000 * 1000); // delay for LCD to wake up
    
    lcdinit();
    while (1)
    {

        Put_String(0, 1, "Hello, World!");
        Put_String(0, 2, "My name is yingshaoxo.");
    }
}
```

## References:

{% embed url="https://circuitdigest.com/microcontroller-projects/graphical-lcd-interfacing-with-arduino" %}

{% embed url="https://www.instructables.com/id/The-Secrets-of-an-Inexpensive-Ubiquitous-Chinese-L/" %}

{% embed url="https://blog.csdn.net/coderwuqiang/article/details/9181853" %}


