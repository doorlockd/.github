# Doorlockd
Doorlockd is a system for controlling (access to) electronic door locks.
It was built for use in [De WAR](https://www.dewar.nl), a community
building in the Netherlands, but can probably be useful in other places
as well.



It is designed to control an electronic lock (we are using it to control
an electric strike, but it should be possible to use any 12V or
24V-controlled lock, including electric deadbolts or magnetic locks).

Authorization is done using an NFC card reader (using the PN532 chip),
using simple card-id comparison.

Typically each lock (and card reader) will be handled by a small
Linux-based single-board computer (we use a Beaglebone black) with an
add-on PCB to add the needed connectors, switch FETs and other needed
hardware.

Management is done using a Django-based backend and web interface.
A single backend can control many locks - each lock runs its own
lock-client that pulls authenticated card ids from the backend
periodically. The backend can run on any hosting environment that can
run Django, including on one of the lock beaglebones.

Features:
 - Flexible module-based approach and event-based configuration allowing
   to support many different usecases out of the box, and additional
   cases by writing extra modules. Typical usecases:
   - Unlock door when valid card is presented
   - Unlock door when button (inside the building) is pressed
   - Lock and unlock the door by pressing a button
   - Detect when the door is opened or locked
 - Unknown cards are tracked in the backend and can be authorized
   easily.
 - Runs on any Linux-based SBC or regular computer.
 - Supports PN532 NFC card reader via UART (other card readers could be
   added).
 - Supports Linux GPIO pins for buttons and leds.
 - Supports any lock that can be controlled using a GPIO pin (typically
   12V or 24V controlled using a FET switch).

The system consists of four components, each of which are in their own
repository.
 - [doorlockd-backend](https://github.com/doorlockd/doorlockd-backend)
   is the Django-based python backend.
 - [doorlockd-client](https://github.com/doorlockd/doorlockd-client) is
   the python daemon that runs on each lock
 - [doorlockd-PCB-Reader](https://github.com/doorlockd/doorlockd-PCB-Reader)
   is a small PCB on top of the [Elechouse PN532 card
   reader](https://www.elechouse.com/product/pn532-nfc-rfid-module-v4/)
   and adds a convenient 4-pin connector for the UART, some leds for
   user feedback and an auxillary input and output pin.
 - [doorlockd-PCB-BBB](https://github.com/doorlockd/doorlockd-PCB-BBB)
   is a a PCB on top of the Beaglebone black that adds various
   connectors, a power supply and FET-switches for lock outputs.

See the individual repositories for additional details on these
components and how they fit together.

Installation documentation is currently a bit limited, but we use
Ansible to install and configure the lock clients and backend, which can
be used as an example for your own deployment and provides good insight
in the steps needed to setup the system as well. The ansible files can
be found in [this
repository](https://github.com/PlanBCode/doorlockd-ansible-config).

There is also a 3D-printed case for the card reader, but the design
files for that are not yet public.

### Example deployment
To get a better overview of what is included, our main entrace deployment is shown below.

On the outside, there is a card reader next to the door:
![Card reader on door](https://github.com/doorlockd/.github/assets/194491/218221fb-ecee-4535-a2cc-60ebb9f1cd57)

On the inside, there is a control board above the door (just bare PCB for now), and a big button to unlock the door:

![Door overview](https://github.com/doorlockd/.github/assets/194491/09cbde6a-a260-4dfc-8ede-d976ae17f60e)
![Control board](https://github.com/doorlockd/.github/assets/194491/db00c397-54ba-42a7-a7d9-d495e168d11c)

This particular door uses an electric strike ("sluitkom" in Dutch) on the deadbolt ("nachtschoot") instead of the latch ("dagschoot"), so the door is more firmly locked and can have a door handle (instead of a knob) on the outside (though the door also has an electric strike for the latch, but we do not use that currently). Here is the electric strike (English terminology might be wrong, though):

![Electric lock](https://github.com/doorlockd/.github/assets/194491/f91da646-d37c-4d6d-a182-a1069856c648)

The inside of the cardreader looks like this:

![Card reader right side](https://github.com/doorlockd/.github/assets/194491/c7c6916a-b95a-4820-a532-029078ff619e)
![Card reader left side](https://github.com/doorlockd/.github/assets/194491/eda94f71-e920-42ce-8465-c874aa935430)
![Card reader top](https://github.com/doorlockd/.github/assets/194491/359c1e7b-0808-41de-8270-e5aa8f5a7691)

The configuration and installation of this system is managed via an ansible playbook, which can be found at https://github.com/PlanBCode/doorlockd-ansible-config
