# Power system

*Estimated time: 30 minutes*

This page describes how to set up the power system, which supplies power from the sealed lead acid (SLA) battery to the motors and encoders.

## Power cable assembly

Tools:

* 5mm hex key
* 5/64" hex key
* Socket wrench with 10mm socket
* Electrical tape
* Zip ties

Follow these steps to assemble the power distribution panel (PDP) and power cable:

1. Connect the power cable and 120A circuit breaker to the PDP.
1. Use electrical tape to cover any exposed connections and secure the tape with zip ties.
1. Install the eight 40A breakers into channels 0-3 and 12-15.
1. Install the 5A breaker into channel 4 along with the [terminal spacer](https://github.com/jimmyyhwu/tidybot2-resources/blob/main/3D%20Printing/Breaker%20Terminal%20Spacer.stl).

See video (4x speed):

<video data-src="../videos/power-system/IMG_4764-4x.mp4#t=0.001" controls playsinline></video>

!!! note

    Make sure that all power cable connections are tight! Loose connections can be very difficult to troubleshoot.

!!! note

    The 40A breakers should be fully inserted with no exposed metal:

    ![](images/power-system/IMG_6657.jpg){ width="49.45%" }

!!! note

    The 5A breaker needs a terminal spacer because its terminals are too long (left photo).
    The spacer covers the exposed terminals to prevent accidental shorts (right photo).

    ![](images/power-system/IMG_6654.jpg){ width="49.45%" }
    ![](images/power-system/IMG_6656.jpg){ width="49.45%" }

!!! note

    Use electrical tape to cover all exposed metal:

    ![](images/power-system/IMG_4780.jpg){ width="49.45%" }

!!! tip

    Double-check the polarity of the power cable connections.
    Red goes with plus and black goes with minus.

!!! tip

    The [FRC robot wiring guide](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-1/intro-to-frc-robot-wiring.html) may also be helpful.

## Battery assembly

Tools:

* Socket wrench with 5/16" socket
* Adjustable wrench
* Electrical tape
* Zip ties

Follow these steps to assemble each SLA battery:

1. Connect a battery cable using the screws, nuts, and washers that came with the battery.
1. Use electrical tape to cover the exposed terminals and secure the tape with zip ties.

See video (4x speed):

<video data-src="../videos/power-system/IMG_4762-4x.mp4#t=0.001" controls playsinline></video>

!!! note

    Make sure the battery cable connections are tight! Loose connections can be very difficult to troubleshoot.

!!! note

    Here is how the cable should be oriented for the smaller battery:

    ![](images/power-system/IMG_1545.jpg){ width="49.45%" }

!!! note

    Use electrical tape to cover all exposed metal:

    ![](images/power-system/IMG_4777.jpg){ width="49.45%" }

!!! tip

    Double-check the polarity of the battery cable connections.
    Red goes with red and black goes with black.

## Motor power

Tools:

* WAGO tool

Connect the power cables from all eight motors to the PDP.
See video (4x/16x speed):

<video data-src="../videos/power-system/IMG_6709-4x-16x.mp4#t=0.001" controls playsinline></video>

!!! note

    In this step, we are temporarily wiring the motors and encoders for bench testing.

!!! note

    Some people may find the WAGO connectors on the PDP challenging to work with.
    Here is a close-up video demonstrating their usage (2x speed):

    <video data-src="../videos/power-system/IMG_6687-2x.mp4#t=0.001" controls playsinline></video>

    As shown in the video, the WAGO operating tool should be inserted straight into the actuation slot at a slight angle to horizontal.
    The end of the tool should be pushed downward to open the slot fully.

    We also recommend watching this video for more information: [WAGO connectors explained](https://www.youtube.com/watch?v=t-zb7j4ikHM).

!!! note

    **Do not forcefully wiggle the WAGO tool or pry it upward!**
    Doing so can deform the plastic housing, making the connector harder to operate in the future.

!!! tip

    If you are having trouble getting the wires to go all the way in, look inside the wire insertion slot.
    Since the wires are thick, it is not sufficient for the slot to be partially open (left), it needs to be fully open (right):

    ![](images/power-system/IMG_6698.jpg){ width="49.45%" }
    ![](images/power-system/IMG_6700.jpg){ width="49.45%" }

## Encoder power

Tools:

* WAGO tool
* Wire stripper (22 AWG solid)

Follow these steps to build the encoder wiring harness:

1. Cut three 22AWG cables: one at 45cm and two at 30cm.
1. Strip 1 cm of insulation from each end.
1. Use two 5-port wire connectors (one for red, one for black) to connect the wires.
1. Attach inline wire connectors to the free ends of the two 30cm cables.

See photos for reference:

![](images/power-system/IMG_6705.jpg){ width="49.45%" }
![](images/power-system/IMG_6708.jpg){ width="49.45%" }

!!! note

    Be sure to strip at least 1 cm of insulation.
    If there is not enough exposed wire, the WAGO connector may clamp onto the insulation, resulting in intermittent power to the encoder.

Next, use the wiring harness to connect all four encoders to the PDP.
See video (2x speed):

<video data-src="../videos/power-system/IMG_6710-2x.mp4#t=0.001" controls playsinline></video>

## Setup complete

At this point, the power system setup is finished, and you should be ready to connect the SLA battery.
Make sure to double-check the polarity of all power connections (power cable, battery cable, motors, and encoders) before proceeding.
Once the battery is connected, all motors and encoders should light up, indicating that they are powered on.

!!! note

    For battery charging instructions, please see the [Usage guide](usage.md#power-off-procedure).
