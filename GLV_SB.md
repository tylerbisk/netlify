---
layout: page2
title: GLV Shutdown Box
permalink: /fsae/glv_sb
share-img: "/assets/img/glvsb.png"
---


<div align="center">

    <img src="{{ "/assets/img/curacing.png" | relative_url }}" style="width: 30%"/>
    <p></p>
    <h1>Grounded Low Voltage Shutdown Box</h1>
    <h3>(GLV SB)</h3>
    <p></p>
    <img src="{{ "/assets/img/glvsb.png" | relative_url }}" style="width: 98%"/>
</div>
<div>
    <br>
</div>

##### Introduction
The function of the GLV_SB is to safely turn off the car when a variety of conditions are met while the car is driving.  If a fault is detected somewhere within the car, the tractive system will be shut down.  This board is necessary because it ensures that if there is some sort of failure when driving, the car is immediately turned off which minimizes damage to the driver, the crew, or the car itself.

There are many rules regarding car safety and shutdown circuitry that must be complied with in order to race in competition.  In summary, my board must detect isolation failure, temperatures and voltages outside an allowable range, and simultaneous braking and acceleration inputs.  If one of these faults are detected, an indicator light must illuminate while immediately cutting power to and from the accumulator.

It is my responsibility to make sure that my board exceeds what is specified by the rules in order to be confident that we will get to competition with a rules compliant car.  There are four pages of rules pertaining to the GLV Shutdown Box.  You can read about them [here](https://www.fsaeonline.com/cdsweb/gen/DownloadDocument.aspx?DocumentID=6d9f4b51-a642-425c-bfdf-5f95b4e5e10b){:target="_blank"} if you'd like (pages 95-98).


##### Research
In order for my board to detect when there is or is not a fault, the board has five inputs, each indicating the status of other systems.  If any of these signals indicate a fault or error that meet the criteria in the rules listed above, the corresponding latching circuit opens open and disables the tractive system.  Below is a list of signals that I will be monitoring, with the type of fault it outputs.

- Multipurpose Enable
    - Open Drain [1]
- Discharge Enable
    - Open Drain [1]
- Accumulator Current
    - Active High
- Brake Pressure
    - Active High
- IMD Fault
    - Active Low + Pull Down [2]

The idea behind the board is to design a latching circuit that opens the shutdown circuit when any one of these five faults are received while simultaneously closing a circuit that will illuminate an LED.  The respective circuits must remain open and closed until an active high RESET signal is received.


##### Design
There is a very short history of the GLV Shutdown Box.  The GLV_SB was first designed in 2019 by Nicole Lin and Jon Gao [3].  Nicole and Jon’s design first was just a BSPD, but they realized that one board could handle all of BSPD, IMD, and AMS faults.  Once they integrated those features, there were three separate latching circuits on the board on their rev2.  Their design did not pass rules, COVID sent us home from school, and I was asked to continue the project.

At the beginning of Fall 2020, I began work on the GLV_SB.  I noticed a few errors in the design that was passed down to me from Nicole and Jon.  I researched solutions that I believed would work and eventually translated those ideas into reality by completely redoing the Shutdown Box in *Altium Designer*.  Unfortunately due to the nature of competition, I cannot go into more detail on the logic behind my board or post the schematics.

##### Board Bring-up and Testing
After design, it is time to bring up the PCBs.  I luckily was able to bring up a couple boards in November to begin my thorough testing plan.  I already had some practice with board bring up, so I brought in someone on the team with less experience to act as my assistant so I could transfer my knowledge and mentor them.  After applying solder paste, delicately placing the parts on, and sending it through the reflow oven, it was time to test for functionality.  Unfortunately, none of the three latching circuits were working as expected.  After some careful debugging, I discovered that the source and drain on the MOSFETs footprints were the opposite as what was expected.  Since the components are so small, the only option is to order new boards.


##### Revision 2
After correcting the mistake, I am confident that the board will latch perfectly.  I took the opportunity to rearrange some components and reroute some traces - nothing major.  I will update this page in January when these Rev 2 boards come in and I can test them in the lab.
