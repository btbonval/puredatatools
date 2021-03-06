# About

The files contained in this repository are experiments for generating
electronic music in Pure Data (Pd).

Pd-extended is not used for this repo, so some of these patches might be a
little bit over the top when Pd-extended might offer similar capability in
an easier format.

# File Hierarchy

## Why no directories?

Directories would be nice, but Pure Data requires each path to be a single
directory and does not dive into subdirectories. This repository is meant to be
used as a single search path for Pure Data, mostly because that is easier than
adding a bunch of paths to subdirectories as needed or desired.

## Modulators

Some of the typical modulators. AM, FM, etc.

### Files

#### `_test_AMmod.pd`

Shows how to use `AMmod~`. Two `AMmod~`s are used to show daisy chaining the
modulation, and some buttons exist for interesting settings.

* uses
 * `AMmod~`
 * `fullphasor~`

#### `AMmod~.pd`

Modulates the left carrier signal by the right modulator signal. Carrier and
modulator signal are both expected to be full amplitude -1..1.

## Custom wave generator

This set of patches is inspired by the custom wave chapter in the FLOSS Pure
Data manual.

There seems to be a superfluous warning about
`0-custom-wave-shape: no such object`. This is due to the use of `$0` in an
array, but the array and pd commands all function as expected.

### Files

#### `_test_wavegenerator.pd`

Shows how to use `wavegenerator~`. Two `wavegenerator~`s are used to show
independence between the abstractions (related to notes about `$0` above).

One has a bunch of sliders to configure it, the other has two options submit
by way of messages.

* uses
 * `wavegenerator~`

#### `wavegenerator~.pd`

Creates custom waveforms by specifying a bit resolution for the wave, a base
frequency to play the wave at, and some `sinesum` parameters to shape the wave.

* uses
 * `handle8args`
 * `hardbounds`
* todo
 * condense sinesum parameters into single inlet via pack/list?

#### `hardbounds.pd`

Constrains a value between a minimum and maximum value. It's helpful for
abstractions where user controls might be configured externally, because
the controls might not respect the proper value extents expected by the
abstraction.

If the test value goes below the minimum value, the minimum value is output
instead. If the test value goes above the maximum value, the maximum value is
output instead.

* todo
 * use `handle3args`

## Handling arguments

As a programmer, I see each block as a function with inputs and outputs. There
are three ways to push input arguments into these functions:

1. inlets
1. a list of arguments in the object box
1. a list-message to the first inlet

I wanted to make an abstraction which would handle all three cases in the same
way. Wire up your inlets directly to an abstraction and then wire up the
outlets of the abstraction as if they were the inlets.

### Files

#### `_test_handle3args.pd`

Demonstrates how `handle3args` works. Shows many ways to connect to the 
`handle3arg`'s inlets, with simple output of the outlets.

* uses
 * `handle3args`

#### `handle3args.pd`

Strongly commented argument handler. Supports 3 arguments by way of 3 inlets,
object-level arguments, and/or a list of 3 passed to the first inlet.

#### `handleXargs.pd`

Unfortunately, I have not figured out how to generalize this solution to
support an arbitrary number of inlets/outlets from a single abstraction. Each
number of arguments will need its own abstractions. Whichever abstractions
are present are because they were needed.

If you don't feel like making your own but there is one with more arguments
than you need, use the abstraction with more arguments. Just ignore the extra
inlets/outlets.

## Random Noise Generator

The idea is to use simple, familiar wave functions which are produced at
randomly sampled frequencies.

This is not yet advanced enough to do 1/f noise or that sort of thing.

### Files

#### `_test_noisegenerator.pd`

Shows an example of hooking up to `noisegenerator~`.

* uses
 * `radio2symbol4noisegen`
 * `noisegenerator~`
 * `stereomixer~`

#### `radio2symbol4noisegen.pd`

Converts floats into symbols of `off`, `sine`, `square`, `saw`. Auxiliary.

#### `noisegenerator~.pd`

Generates one of three wave types at a specified frequency.

Changing the frequency can be performed by an internal oscillator or external
bang.

* uses
 * `fullphasor~`
 * `square~`
 * [`lnrnd4Hz`|`unifrnd4Hz`]

#### `lnrnd4Hz.pd`

Randomly samples floats from a distribution close to the log normal
distribution.

The extent of the floats are meant to be interpreted as Hz within human hearing
capability. These might need to be opened up for younger persons with higher
frequency reception or with better performing speakers.

#### `unifrnd4Hz.pd`

Randomly samples floats from the uniform distribution.

The extent of the floats are meant to be interpreted as Hz within human hearing
capability. These might need to be opened up for younger persons with higher
frequency reception or with better performing speakers.

## Tools

Miscellaneous tools.

### Files

#### `_test_crossfader.pd`

Tests the crossfader by alternating between two stereo tones.

* uses
 * `stereocrossfader~`

#### `stereocrossfader~.pd`

Takes in two stereo inputs and crossfades between them. Left two inlets are
deck A channels, next two inlets are deck B channels, and the right most inlet
is a ratio of B from 0..1.

0 will output only deck A's channels, 0.5 will evenly mix decks A and B, and 1
will output only deck B's channels.

* uses
 * `hardbounds`

#### `square~.pd`

Modifies a fullphasor into a binary square wave.

#### `fullphasor~.pd`

Extends phasor amplitude from 0 to 1 to -1 to 1, for audio saw
waves.

#### `stereomixer~.pd`

Inputs a master gain for both channels, 2 audio channels, and independent
gain for each channel.

Inside the abstraction, volume meters display each channel's dB and waveforms
can be visualized with an oscilloscope-like function.

* todo
 * try to overlay independent gain control floats into the audio inlets
 * use `hardbounds`

#### `sin~.pd`

A sine function to complement the `cos~` cosine function.
