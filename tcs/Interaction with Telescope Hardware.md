# Interaction with Telescope Hardware

## telescopecontroller

### Status

We need to know:

- The error status. This seems to be obtained by `A016`. The response is a two-digit code described in the table on page 7 of the English document.

- The emergency stop status. This seems to be obtained from bit 0x10000000 of the response from `A371`. I presume this bit is set to 1 when the emergency stop is activated. 

- The power status. This *might* be described by the `A371`. In the table on page 9 of the English documents, one of the bits of the response is described as “Turn off”. It is important to understand is 0 or 1 mean turned off. TBC.

- The telescope mode. Specifically, if it is in “tracking” or robotic mode. This is confirmed by the bit 0x0002 of the response from `A017` being 1.

- In COLIBRÍ, we read telescope temperature sensors through the telescope controller. The TP2m telescope does not seem to provide this.

### switchon

This should switch on the telescope motors and change the telescope controller status so that it will accept commands to move telescope mechanisms.

No command sequence for switching on the  is documented, but I presume one must exist.

### switchoff

This should switch off the telescope motors and change the telescope controller status so that it will not accept commands to move telescope mechanisms. Sensor values and status need to still be available.

The appropriate command sequence seems to be `O`.

### waitutilswitchedon

This should wait until the telescope is switched on.

I presume we poll the response from `A371` until the “Turn off” bit changes appropriately.

### waitutilswitchedoff

I presume we poll the response from `A371` until the “Turn off” bit changes appropriately.

### stop

This should be a soft stop, which allows time to ramp down motions. In some telescopes, this is not possible and only a hard stop is available.

The appropriate command would seem to be `S` and `C`.

It is not clear if `S` as a soft stop or a hard stop. It is also not clear if we need to stop the dome as well with `DE` and `DS STOP`.

### emergencystop

This is supposed to be a hard stop, without allowing time to ramp down motions. In some telescopes, this is not possible and only a soft stop is available.

The appropriate command would seem to be `S` and `C` or possible `O`, `DE`, and `DS STOP`.

It is not clear if `S` as a soft stop or a hard stop. 

### waituntilstopped

This should wait until the telescope is stopped, either by a soft or hard stop.

I would guess that to determine when the `S` command has stopped the telescope, we need to poll the result of `A090` (move status) until it is -1 (“stop”). However, bit 0x0004 of the response from `A017` is labeled as “moving” and might also be relevant.

I would guess that to determine when the `O` command has powered down the telescope, we need to use the procedure for waituntilswitchedoff.

It's not clear how we determine whether the dome and dome shutters have stopped. Perhaps there are flags in the response from `A121`.

We might have to have separate tasks for normal stops and emergency stops.

### reset

This recovers from minor errors.

The appropriate command would seem to `E`.

### waituntilreset

I presume we poll the result of `A016` until it is 0.

### resetpanic

This recovers from panics (e.g., after an emergency stop is pressed and released). It is not used in normal operation, but is available in engineering mode.

This functionality might not be available or needed.

## mount

I am not currently concerning myself with rotation offsets, since these are typically not needed for imagers. However, they will be needed for the spectrograph.

### Status

We need:
- RA and Dec. We can obtain these by adding the results of `A019`, `A021`, `A050`, and `A051`. I presume these are J2000.
- Azimuth and zenith distance. We can obtain these from the results of `A010` and `A012`.
- Selected port. We can obtain these from bits 0x7800 of the result of `A017`.

### emergencystop

This is the equivalent to the telescopecontroller command above, but is only relevant for telescope where the mount is independent of the other components. I don't think this applies to TP2m.

### stop

This is the equivalent to the telescopecontroller command above, but is only relevant for telescope where the mount is independent of the other components. I don't think this applies to TP2m.

### waituntilstopped

This is the equivalent to the telescopecontroller command above, but is only relevant for telescope where the mount is independent of the other components. I don't think this applies to TP2m.

### setportposition

This should select a new port. It won't necessarily move to the port until a move or track command is issued, but it might.

We have three ports:

- Cassegrain. Selected by the `b` command.
- Nasmyth right. Selected by the `a` command and then the `c` command.
- Nasmyth left. Selected by the `a` command and then the `d` command.

Does this cause immediate movement? How do we know when it is completed? Is the port selection mechanism included in the waituntilontarget procedure above?

### park

This should move the telescope to its parked position. Once parked, TCS will only move the mount in response to an unpack command. (This is implemented in software, not hardware.)

This could either be implemented with the `M` command or with the `Y` command. We will need to know if the `Y` command does more than just move the telescope. We will also need to know how to change the position used by the `Y` command.

### unpark

This should move the telescope to its parked position. Once parked, TCS will only move the mount in response to an unpack command. (This is implemented in software, not hardware.)

This could be implemented with the `M` command.

### initialize

This should prepare the mount to be moved. In some cases, it might be handled by the switchon task of the telescope controller.

We might need to command `Z`, perhaps if `A016` returns 10 (“demand on zero search“). It seems to move only 20 degrees in each axis, so might this need to be repeated if the initial error is more than 20 degrees?

There are also `e` and `f` commands that apparently search for the origins in azimuth and altitude. How are these related to `Z`?

### move

This should move the telescope to a fixed azimuth and zenith distance.

The is implemented with the `M` command.

### trackoroffset

This is used to track a new position. The position is given in RA and Dec. The task should figure out whether it needs to offset or track (slew).

This could be implemented by the `T` and `P` commands. If the telescope is not tracking, `T` should be used. If it is tracking, the software needs to keep track of the latest position sent by `T` and only use `P` if the new position is within 1 degree in both RA and Dec.

### waituntilontarget

This should wait until the telescope and port has arrived at its fixed or tracking target.

I would guess that we need to poll the result of `A090` (move status) until it is 1 (“tracking”). We need to check this also works for slews to fixed positions commanded by `M` and for the home position commanded by `Y`, if we use this.

We might also need to wait until the requested port corresponds to the current port.

## secondary

### Status

We need to know:

- Whether the secondary has been zeroed
- The current position. This is return by `A027` in mm (and needs scaling by 1000 to give integral microns).

### initialize

This should prepare the mount to be moved. In some cases, it might be handled by the switchon task of the telescope controller.

The manual talks about sending `L` to find the secondary zero position when this is lost, but it does not seem to give a means to determine if it is lost or not.

### stop

This is the equivalent to the telescopecontroller command above, but is only relevant for telescope where the secondary is independent of the other components. I don't think this applies to TP2m.

### move

Move to a fixed position. Backlash correction is handled at a higher level. The position must be an integer (so but can be scaled if necessary).

This could be implemented by the `K` command, with scaling by a factor of 0.001 to convert to mm.

### waitwhilemoving

This should wait until a secondary motion has finished.

I would guess that we need to poll the result of `A090` (move status) until it is 1 (“tracking”). I am not sure.

Another possibility is to wait until the secondary position stabilizes or reaches the commanded value.

## dome

### Status

We need to know:
- The azimuth of the dome and possibly if it is moving. This seems to be returned by `A120` in units of 0.1 degrees.
- Whether the shutters are open, closed, or intermediate, and possibly if they are moving. Might this be in the dome status returned by `A121`?

### initialize

This should prepare the dome to be moved.

Do we need to search for a home position? How?

Do we need to set the dome speed with `DM`?

### stop

This is the equivalent to the telescopecontroller command above, but is only relevant for telescope where the dome is independent of the other components. I don't think this applies to TP2m.

### open

This should open the shutters.

It appears that we should use `DS OPEN`.

### close

This should close the shutters.

It appears that we should use `DS CLOSE`.

### emergencyclose

This should close the shutters even if the telescope is in an unusual state. For example, it should turn on the power and clear errors, if necessary.

The sequence we need to use is probably:

    E
    O
    DE CLOSE

We need appropriate waits.

### move

This should move to a fixed azimuth.

We should use `DM`.

## covers

### Status

We need:

- Whether the covers are open, closed, or intermediate, and possibly whether they are moving. The bits 0x0020 and 0x0040 of the result of `A017` indicate if the covers are open or closed. Presumably, if both are zero the covers are intermediate.

### initialize

This is the equivalent to the telescopecontroller command above, but is only relevant for telescope where the covers are independent of the other components. I don't think this applies to TP2m.

I don't think we need anything.

### stop

This is the equivalent to the telescopecontroller command above, but is only relevant for telescope where the covers are independent of the other components. I don't think this applies to TP2m.

I don't think we need anything.

### open

Open the covers.

We should use `G`.

### close

Closed the covers.

We should use `H`.

## lights

### Status

We need to know:

- Whether the lights are on or off. Might this be in the dome status returned by `A121`?

### switchon

Switch on the dome lights.

We should use `DL ON`

### switchoff

Switch on the dome lights.

We should use `DL OFF`
