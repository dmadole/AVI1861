# AVI1861

> [!NOTE]
> I have found another issue with the ATG1861 design that I will be correcting in a revision B  (as well as the couple of revision A errata).
> This is currently being tested and new artwork in production, I will be posting more information as that progresses.

This is an update of the [AVI1861](http://www.cosmacvip.com/avi1861/avi1861.html) which is a drop-in replacement for the obsolete RCA [CDP1861](http://www.cosmacelf.com/publications/data-sheets/cdp1861.pdf) video chip. While the CDP1861 was a very low-cost way to produce video, it was rather low resolution and had other severe limitations such as being tied extremely closely to the RCA1802 microprocessor. Thus, although it was used in many iconic early systems, it was never used in anything with mass-market success and went out of production in a few years and is very difficult to obtain today.

![Assembled AVI1861 Rev A](https://github.com/dmadole/AVI1861/blob/main/photos/avi1861-rev-a-assembled.jpg?raw=true)

The AVI1861 itself originated in the [STG1861](http://www.sparetimegizmos.com/Hardware/Elf2K_Accessories.htm#STG1681%20Pixie%20Graphics%20Replacement) designed by Robert Armstrong for his [Elf 2000](http://www.sparetimegizmos.com/Hardware/Elf2K.htm) system around 2004 to allow users to support CDP1861 graphics even if they could not obtain an actual chip. It is a mostly compatible replacement for the CDP1861 made from a couple of 22V10 PLDs plus a counter and a shift register, comprising four chips alltogether. While the STG1861 was advertised as a replacement for the CDP1861 in any application, it had a disadvantage of being much larger which could make it awkward on other machines which did not have the space and mounting holes that the Elf 2000 did.

In around 2015, Ed Keefe repackaged the STG1861 design using surface mount parts to reduce the size to that of a 24-pin DIP package so it would fit exactly in the original footprint of a CDP1861 chip. Aside from the change to SMD parts and the addition of an RC filter on the video pin to reduce the effect of a timing glitch, it was the same design as the STG1861 and used the same PLD programming. Ed called his new version the AVI1861 from the mark AVI he used for Audio Visual Imagery, his design company.

Sadly, Ed passed away in 2022. Although care has been taken to preserve and continue much of his work, the design files for the AVI1861 could not be found, so as a service to the COSMAC Elf community, I volunteered to recreate the design so that others could enjoy his idea, and this was how my version of it came about. I have corresponded with Ed many times in the past including about the AVI1861, and I have a copy of it that he built himself and gifted to me.

As I started to duplicate the design, through discussion in the [COSMAC ELF Group](https://groups.io/g/cosmacelf/) I found there were a few flaws going back to the STG1861 that made it just a slightly imperfect replacement for the CDP1861.

This started when I noted that Ed had added an RC network to the video output; when I mentioned it to the group it was revealed that was perhaps to filter out a vertical glitch that happened when the shift register was loaded. The likely reason for this is that a 74HC165 is used for the shift register, which has an asyncronous paralallel load enable, yet the load of the byte needs to be closely syncronized with the pixel clock. In the STG1861 this syncronization was done in the PLDs but due to the different propagation delays this could never be implemented perfectly. It may have also been contributed by the shift register being an HCMOS part with a faster and stronger output drive than the CMOS of the original CDP1861, complicated by the fact this feeds analog output circuitry which shows small glitches that would be irrelevant to a digital system.

I decided to correct this issue by switching to a CD4014 shift register, which has a load enable that is syncronous to the clock input, so that they will be exactly syncronized internally. Additionally, being CMOS rather than HCMOS would more closely replicate the output drive of the original chip. This change required modifying the PLD programming to invert the state of the paralell load signal, since it is the opposite of the 74HC165.

Other discussion and feedback from the group was that the AVI1861 did not work in the RCA Studio II game console, with Bob Armstrong himself suggesting that this was probably due to the RESET- input not being a Schmitt trigger like the original part. So I added a 74LVC1G17 Schmitt trigger buffer to that pin to better replicate the original behavior.

Lastly, two of the pins on the CDPO1861, INT- and DMAO- were open-drain outputs, and on the STG1861 this was accomplished by putting diodes in series with the PLD outputs so they could only drive low. Although I didn't know of any instance where this was an actual problem, as long as I was updating the design I replaced these diodes with two true open-drain gates using a 74LVC2G07 buffer.

So in the end, this is not an exact reproduction of Ed's work, but rather an improved design based on the same concept and packaging. But I am sure that is how Ed himself would have also gone aboutrecreating it if he had to as well.
