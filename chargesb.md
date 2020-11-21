---
layout: page2
title: Charger Shutdown Box
permalink: /fsae/charge_sb
share-img: "/assets/img/chgsbrev2.png"
---


<div align="center">

    <img src="{{ "/assets/img/curacing.png" | relative_url }}" style="width: 30%"/>
    <p></p>
    <h1>Charger Shutdown Box</h1>
    <h3>(Charge SB)</h3>
    <p></p>
    <img src="{{ "/assets/img/chgsb.png" | relative_url }}" style="width: 78%"/>
</div>
<div>
    <br>
</div>

##### Introduction
The function of the CHARGE_SB is to handle shutting down the charger if a problem arises while charging.  Unlike the GLV_SB, this board is not inside the car, but rather inside the charger.  Like the GLV_SB, the board is essential because it allows us to charge the accumulator.  If a fault is detected, the board shuts down the charger.

A board that handles charging up the battery pack safely is required by rules.  Like the GLV_SB, the CHARGE_SB must detect isolation failure and temperatures and voltages outside an allowable range, but unlike the GLV_SB it does not need to detect simultaneous braking and acceleration inputs because there is no driver when we are charging.  In fact, it is required by rules to remove the accumulator from the car in order to charge it. In the case of a failure, my board must illuminate an indicator LED and turn off the charger.


##### Research
In order for my board to detect when there is or is not a fault, the board has three inputs, each indicating the status of other systems.  If any of these signals indicate a fault or error that meet the criteria in the rules listed above, the corresponding latching circuit opens open and disables the tractive system.  Below is a list of signals that I will be monitoring, with the type of fault it outputs.

- Multipurpose Enable
    - Open Drain [1]
- Charge Safety
    - Open Drain [1]
- IMD Fault
    - Active Low + Pull Down [2]

The board must output a signal to the charger to tell it to turn off if any one of these three faults are triggered, in addition to turning on the corresponding fault LED.

##### Design
I am the first person to be designing a Charging Shutdown Box.  We need two total shutdown boards because one goes inside the car and one goes inside the charger.  Originally, last year’s Electrical lead planned on using a GLV shutdown box as the charger shutdown box.  However, this cannot be done without some modification because the CHARGE_SB must shut down the charger, while the GLV_SB must shut down the accumulator.  Doing so requires subtly different circuitry, but enough to warrant the development of a new board.  Additionally, there is no BSPD on the CHARGE_SB so it can be made smaller and neater.  In any case, there is more than enough reason to design an SB used strictly for charging.

The main challenge was EV.10.4.2.c, “the charger must be turned off” if there is a fault.  The current implementation of the SB only turned off the tractive system, not some external charger.  I referred to the Brusa Technical Manual (the manual for our charger) to figure out if there was a way to turn it off.  It turns out, there is a signal called PON that Powers On the Brusa charger if it is high, and turns it off if it is low [4].  Therefore, a simple fix to my board is to include a pull-down resister before the output of my board and connect that output directly to PON.  This way, if there is a fault and the circuit is open, the resister will cause PON to be set to ground and thus turning off the charger.

##### Board Bring-up and Testing
After design, it is time to bring up the PCBs.  I luckily was able to bring up a couple boards in November to begin my thorough testing plan.  I already had some practice with board bring up, so I brought in someone on the team with less experience to act as my assistant so I could transfer my knowledge and mentor them.  After applying solder paste, delicately placing the parts on, and sending it through the reflow oven, it was time to test for functionality.  When connecting the board to the 12V power supply, a short was discovered between power and ground.  Initially I feared that this was due to a design flaw where I had used incorrect footprints for my components, but after analyzing my layout file I ruled that out because it used all of the same components as the GLV_SB which did not have this problem.

I began to remove components one by one until the short was resolved.  It turned out that one of the components shipped to me had an internal fault between power and ground.  After replacing the component, the board worked exactly as intended and is ready to be connected to the charger.


##### Revision 2
Although my rev1 worked, there is always room for improvement in the world of engineering.  Especially since there were six months until competition at the time of me testing my rev1, I knew I had time to make a rev2 that could potentially be even better.  I changed a few things and made major improvements on the layout by being smaller and neater.  I will update this page in January when my rev2 CHARG_SB arrives and I can test it.


<div align="center">
        <h5>Charger Shutdown Box rev2</h5>
    <img src="{{ "/assets/img/chgsbrev2.png" | relative_url }}" style="width: 78%"/>
    <p></p>
</div>
