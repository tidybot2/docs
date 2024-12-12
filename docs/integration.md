# Integration

*Estimated time: 2 hours*

This page will guide you through the process of installing all components into the frame.

## Rope ratcheting

We will use rope ratchets to secure the following components to the frame:

* Portable power station
* SLA battery
* Mini PC

Below is a demonstration of how to use a rope ratchet to secure a small box to the bottom acrylic plate.
See video (2x speed):

<video data-src="../videos/integration/IMG_6720-2x.mp4#t=0.001" controls playsinline></video>

!!! note

    We use rope ratchets because they allow components to be installed or removed in a matter of seconds.
    For a more permanent setup, custom component mounts can be designed.

## Portable power station

*Estimated time: 15 minutes*

Follow the steps below to secure the portable power station (camping battery) to the frame.

!!! note

    It is important to secure the camping battery to the frame because:

    * We do not want the camping battery to move around or fall out of the base during operation.
    * For controller setup, we need to elevate the wheels off the ground by turning the base on its side.

!!! tip

    Make sure the portable power station is functional before installing it in the frame.
    Check that the AC outlets can power your arm and mini PC.

=== "Kinova"

    Attach the bottom acrylic plate using 10mm screws:

    ![](images/integration/kinova/IMG_6731.jpg){ width="49.45%" }

    Secure the camping battery to the bottom plate using 3 double-looped rope ratchets:

    ![](images/integration/kinova/IMG_6739.jpg){ width="49.45%" }
    ![](images/integration/kinova/IMG_6746.jpg){ width="49.45%" }

    !!! note

        We used these mounting holes:

        ![](images/integration/Portable Power Station Mounting Holes - Kinova.png){ width="49.45%" }

    !!! note

        The AC outlets should be on the right side of the mobile base.

    Here is a view of the bottom of the mobile base:

    ![](images/integration/kinova/IMG_6765.jpg){ width="49.45%" }

    !!! note

        On the underside of the base, perpendicular ropes should be arranged such that they do not touch each other.

    !!! note

        We place the front edge of the camping battery on the bottom plate rather than on the crossbar, as we found this arrangement to be less prone to sliding:

        ![](images/integration/kinova/IMG_6752.jpg){ width="49.45%" }

    !!! note

        Turn the mobile base on its side to test the stability of the mounting.
        If the rope ratchets are sufficiently tight, the camping battery should remain securely in place:

        ![](images/integration/kinova/IMG_6747.jpg){ width="49.45%" }
        ![](images/integration/kinova/IMG_6748.jpg){ width="49.45%" }

=== "Franka"

    Move one of the bottom crossbars to create an opening for inserting the camping battery:

    ![](images/integration/franka/IMG_8634.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8635.jpg){ width="49.45%" }

    Insert the camping battery, then move the crossbar back to its original position:

    ![](images/integration/franka/IMG_8637.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8638.jpg){ width="49.45%" }

    Secure the camping battery to the bottom crossbars using 3 rope ratchets in the front-to-back direction:

    ![](images/integration/franka/IMG_8641.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8645.jpg){ width="49.45%" }

    !!! note

        The 3 ropes fit into these notches on the bottom acrylic plates:

        ![](images/integration/Portable Power Station Mounting Holes - Franka.png){ width="49.45%" }

    !!! note

        The AC outlets should be on the front side of the mobile base.

    Use 4 more rope ratchets in the side-to-side direction to secure the battery to the top crossbars:

    ![](images/integration/franka/IMG_8669.jpg){ width="49.45%" }

    !!! note

        These ropes should be looped around the top crossbars to keep the battery from sliding side to side:

        ![](images/integration/franka/IMG_8674.jpg){ width="49.45%" }

        To maintain compatibility with the top acrylic plate, loop around the crossbars only at these specified locations.

    Here is a view of the bottom of the mobile base:

    ![](images/integration/franka/IMG_8672.jpg){ width="49.45%" }

    !!! note

        On the underside of the base, perpendicular ropes should be arranged such that they do not touch each other.

    Attach the bottom acrylic plates using 4 [support brackets](https://github.com/jimmyyhwu/tidybot2-resources/blob/main/3D%20Printing/Franka/Franka%20Bottom%20Plate%20Support%20Bracket.stl), 10mm screws, and slide-in T-nuts:

    ![](images/integration/franka/IMG_8679.jpg){ width="49.45%" }

    !!! note

        Turn the mobile base on its side to test the stability of the mounting.
        If the rope ratchets are sufficiently tight, the camping battery should remain securely in place.

=== "ARX5"

    Attach the bottom acrylic plate using 10mm screws:

    ![](images/integration/arx5/IMG_1416.jpg){ width="49.45%" }

    Secure the camping battery to the bottom plate using 2 double-looped rope ratchets:

    ![](images/integration/arx5/IMG_2538.jpg){ width="49.45%" }
    ![](images/integration/arx5/IMG_2540.jpg){ width="49.45%" }

    !!! note

        We used these mounting holes:

        ![](images/integration/Portable Power Station Mounting Holes - ARX5.png){ width="49.45%" }

    !!! note

        The AC outlets should be on the front side of the mobile base.

    !!! note

        Turn the mobile base on its side to test the stability of the mounting.
        If the rope ratchets are sufficiently tight, the camping battery should remain securely in place:

        ![](images/integration/arx5/IMG_2545.jpg){ width="49.45%" }
        ![](images/integration/arx5/IMG_2546.jpg){ width="49.45%" }

Once the portable power station is securely attached, plug in the charging cable:

=== "Kinova"

    ![](images/integration/kinova/IMG_6773.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_2709.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2548.jpg){ width="49.45%" }

## SLA battery

*Estimated time: 5 minutes*

Follow these steps to set up the SLA battery mount on the back side of the mobile base:

1. Attach the 3D-printed battery mount to the bottom plate using four 10mm screws and T-nuts.
1. Install a rope ratchet on the bottom plate for securing the SLA battery to the mount.

=== "Kinova"

    ![](images/integration/kinova/IMG_2660.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_2711.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2654.jpg){ width="49.45%" }

Place an SLA battery into the mount and secure it with the rope ratchet:

=== "Kinova"

    ![](images/integration/kinova/IMG_2665.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_2713.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2662.jpg){ width="49.45%" }

Verify that the mount functions properly by turning the robot on its side.
The SLA battery should remain securely fixed:

=== "Kinova"

    ![](images/integration/kinova/IMG_2668.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_2715.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2659.jpg){ width="49.45%" }

## Caster modules

*Estimated time: 20 minutes*

Install the caster modules into the frame according to the following schematic:

![](images/Schematic.png){ width="49.45%" }

!!! note

    This is the caster configuration used by our low-level controller.

For each caster module, use the [alignment jig](https://github.com/jimmyyhwu/tidybot2-resources/blob/main/3D%20Printing/Caster%20Module%20Alignment%20Jig.stl) to position the eight T-nuts:

![](images/integration/IMG_6780.jpg){ width="49.45%" }
![](images/integration/IMG_6782.jpg){ width="49.45%" }

Use eight 10mm screws to secure the caster module to the frame.
See video (2x speed):

<video data-src="../videos/integration/IMG_6783-2x.mp4#t=0.001" controls playsinline></video>

!!! note

    Make sure to use the longer 10mm screws, not the 8mm screws.

!!! note

    We are using the 1/4" tapped holes in the caster module's main plate as M5 clearance holes.

!!! tip

    The screws are not supposed to feel tight until they are fully inserted.
    However, these screw holes are highly sensitive to misalignment.
    If a screw feels stuck, loosen it slightly and wiggle the module to correct the alignment before continuing.

!!! note

    After installing the caster modules, check that each module is completely flush against the frame like in the left photo.
    There should not be a gap like in the right photo.

    ![](images/integration/IMG_6784.jpg){ width="49.45%" }
    ![](images/integration/IMG_6787.jpg){ width="49.45%" }

After installation, the configuration of the caster modules should match the schematic:

![](images/integration/IMG_6793.jpg){ width="49.45%" }

## Power system

*Estimated time: 30 minutes*

=== "Kinova"

    Use zip ties to secure the PDP to the left side of the mobile base:

    ![](images/integration/kinova/IMG_6777.jpg){ width="49.45%" }

    Connect all motor power cables to the PDP and organize them using zip ties:

    ![](images/integration/kinova/IMG_6798.jpg){ width="49.45%" }
    ![](images/integration/kinova/IMG_6801.jpg){ width="49.45%" }

=== "Franka"

    Due to the large size of the camping battery, 8 of the 16 motor cables are too short to reach the PDP.
    Follow these steps to set up extensions for the cables:

    1. Cut eight pieces of 10 AWG wire (4 red and 4 black), each 10–20 cm long.
    1. Connect the wires to the PDP.
    1. Attach a 10 AWG wire connector to the free ends of the wires.

    ![](images/integration/franka/IMG_6668.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_6685.jpg){ width="49.45%" }

    Use zip ties to secure the PDP to the left side of the mobile base.
    Then connect all motor power cables to the PDP and organize them using zip ties:

    ![](images/integration/franka/IMG_8704.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8705.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8706.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8707.jpg){ width="49.45%" }

    !!! tip

        Wires can easily detach if not fully inserted into the connectors.
        Check that all connections are secure by giving each one a tug:

        <video data-src="../videos/integration/IMG_6703.mp4#t=0.001" controls playsinline style="width: 49.45%;"></video>

    The 120A circuit breaker can stick out of the frame (left photo), which can cause the motors to shut off unexpectedly if the red manual trip button is accidentally bumped.
    To avoid this, secure the breaker to the camping battery with a zip tie (right photo).

    ![](images/integration/franka/IMG_8717.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8829.jpg){ width="49.45%" }

=== "ARX5"

    Use zip ties to secure the PDP to the left side of the mobile base:

    ![](images/integration/arx5/IMG_2550.jpg){ width="49.45%" }

    Connect all motor power cables to the PDP and organize them using zip ties:

    ![](images/integration/arx5/IMG_2553.jpg){ width="49.45%" }
    ![](images/integration/arx5/IMG_2555.jpg){ width="49.45%" }
    ![](images/integration/arx5/IMG_2556.jpg){ width="49.45%" }

!!! tip

    Double-check all PDP connections and make sure there is no exposed metal, as shown with the black wire in this photo:

    ![](images/integration/IMG_6701.jpg){ width="49.45%" }

Connect all encoder power cables to the PDP using the previously assembled wiring harness:

=== "Kinova"

    ![](images/integration/kinova/IMG_6903.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_8737.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2562.jpg){ width="49.45%" }

## CAN system

*Estimated time: 10 minutes*

Secure the CANivore to the portable power station using a zip tie.
Connect the CANivore to motor 1 and the termination resistor to motor 8:

=== "Kinova"

    ![](images/integration/kinova/IMG_6815.jpg){ width="49.45%" }
    ![](images/integration/kinova/IMG_6817.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_8710.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8712.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2563.jpg){ width="49.45%" }
    ![](images/integration/arx5/IMG_2564.jpg){ width="49.45%" }

Connect the remaining CAN bus cables and organize them using Velcro cable ties:

=== "Kinova"

    ![](images/integration/kinova/IMG_6915.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_8742.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2566.jpg){ width="49.45%" }

## Mini PC

*Estimated time: 5 minutes*

Attach the mini PC to the frame using rope ratchets, zip ties, and/or frame gussets.
Plug the power adapter into an AC outlet on the portable power station:

=== "Kinova"

    ![](images/integration/kinova/IMG_6827.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_8724.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2573.jpg){ width="49.45%" }

Install the USB hub and HDMI extender, which are useful for connecting a monitor, keyboard, and mouse to the mini PC.

!!! note

    Make sure all cables are securely fastened to prevent them from accidentally getting caught in the caster module gears.

## Integration testing

Your mobile base should now be ready for integration testing with the software.
Before proceeding, please perform the following checks:

* Double-check the polarity of all power and CAN connections.
* Verify that all connections are firmly attached by gently tugging on each one.

If everything checks out, please proceed to the [Software](software.md) page for further setup.

Once the mini PC and codebase setup is complete, we typically perform an integration test using [gamepad teleoperation](usage.md#gamepad-teleoperation).
We turn the base on its side and verify that the casters move as expected:

<video data-src="../videos/usage/IMG_6867.mp4#t=0.001" controls playsinline></video>

After confirming that the mobile base is operational, please return to this page to complete the remaining integration steps.

## Cable organization

*Estimated time: 5 minutes*

Use cable sleeves to further organize the CAN wiring:

=== "Kinova"

    ![](images/integration/kinova/IMG_6929.jpg){ width="49.45%" }
    ![](images/integration/kinova/IMG_6925.jpg){ width="49.45%" }

=== "Franka"

    ![](images/integration/franka/IMG_8759.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8761.jpg){ width="49.45%" }
    ![](images/integration/franka/IMG_8752.jpg){ width="49.45%" }

=== "ARX5"

    ![](images/integration/arx5/IMG_2567.jpg){ width="49.45%" }
    ![](images/integration/arx5/IMG_2568.jpg){ width="49.45%" }
    ![](images/integration/arx5/IMG_2571.jpg){ width="49.45%" }

## Base camera

*Estimated time: 5 minutes*

Follow these steps to install the Logitech camera to the front of the mobile base:

1. Insert the 3D-printed shim into the hinge to lock the camera angle.
1. Place the camera between the top and bottom parts of the 3D-printed camera mount.
1. Secure the mount to the frame using two 10mm screws and roll-in T-nuts.
1. Route the USB cable to the mini PC and plug it in.

![](images/integration/IMG_1576.jpg){ width="49.45%" }
![](images/integration/IMG_6990.jpg){ width="49.45%" }
