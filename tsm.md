---
layout: page2
title: Temperature Sensing Module
permalink: /fsae/tsm
share-img: "/assets/img/fsae/tsmlay.png"
---


<div align="center">

    <img src="{{ "/assets/img/fsae/curacing.png" | relative_url }}" style="width: 30%"/>
    <p></p>
    <h1>Temperature Sensing Module</h1>
    <h3>(TSM)</h3>
    <p></p>
    <img src="{{ "/assets/img/fsae/tsmlay.png" | relative_url }}" style="width: 78%"/>
</div>
<div>
    <br>
</div>

##### Introduction
There are 600 18650 li-po batteries in the accumulator - 100 modules of 6 batteries in series.  Rules states that we must be monitoring at least 20% of these modules for overheating, defined as > 60ºC.  Monitoring too few may leave a temperature fault only present in a few cells undetected.  

My idea for one way to implement such a board is to input thermistor readings from the battery cells, then have my board determine whether or not any given signal is > 60ºC. The board will then output an active high fault signal to be fed into the Shutdown Box.

##### Research and Design
This PCB was the first PCB I'd ever done.  Part of my onboarding freshman year included learning about Altium Designer and circuit diagrams, but now it was time for me to show off what I'd learned.  I was given the abstract of what my board had to do but the rest of the details were left up to me. I did the bulk of the research in December and designed it in January.  Per rules, we need to shut down the car if any one of the cells gets too hot.  Therefore, I was able to use multiplexers with the thermistor readings as the inputs.  The select bits came from an STM Microcontroller that I would program to increment the select bit by one on every tick of the clock.

The outputs of the multiplexers fed into digital inputs on the Microcontroller.  The Microcontroller would then be programmed to output high if any of the muxes were outputting a voltage that corresponded to > 60ºC.

As a team, we ended up deciding that monitoring 60% of the cells was optimal for our accumulator, which lent itself to 5 16-to-1 multiplexers with inputs 1-12 in use.

<div align="center">
    <p></p>
    <h5>TSM Schematic</h5>
    <img src="{{ "/assets/img/fsae/tsmskmtc.png" | relative_url }}" style="width: 100%"/>
    <p></p>
</div>


##### COVID-19 and Discontinuation
In March 2020, shortly before TSM bringup and testing would have commenced, all students were sent home for COVID-19.  Since we lost so much in-person time, we decided it would be best to let the Orion Battery Management System act as the TSM for our 2020 car.  The Orion BMS is an off-the-shelf system that can handle temperature and voltage faults in the accumulator.  We hope to replace the Orion with a student-designed BMS as early as Fall/Winter 2021, making Cornell Racing's electrical system completely designed and manufactured by students.

Although the TSM did not make its way into existence or onto the car, I learned all about the research and design process that go into making a PCB and allowed me to be better prepared for my future projects.
