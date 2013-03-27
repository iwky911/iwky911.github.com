---
layout: post
title: WhistleLauncher, launching commands by whistling
category: posts
---

I found recently a post by the engineers at IBM about a way to [command your computer by whistling][ibm].
Whistle launcher is my version of this program, written in Go.

The goal was to detect sequence of sound and be able to detect preconfigured sequences of sounds.
To achieve this goal, the program is build with four main components:

* The sndpeek parser
* The note detector
* The sequence detector
* The sequence matcher

### Sndpeek parser

[Sndpeek][sndpeek] is an audio visualization program developped by the sound lab at Princeton University. It creates real time visualization of sounds and can be used as a blackbox to extract important metrics from the input sound. In particular, launching the program with `--nodisplay --print` will output a list of metrics computed from the mic input stream. Only two numbers are interesting in this output, the third and the fourth respectively rolloff at 50% and rolloff at 80%. Those value represent the frequency below which all the sound energy is. We can then use thoses value to detect a clear note (one tone) by detecting when the two rolloff are close together (all the energy is concentrated on one frequency).

### Note detector

The note detector use the output of Sndpeek (rolloff1 and rolloff2) and outputs notes. To do so, it looks for sequences of close rolloffs. In order to allow some mistakes in a note (the note might not be always clear but should be "almost" always clear), we use a simple state machine to allow notes with isolated non clear values.

### Sequence detector

The goal of the sequence detector is to detect valid sequences of notes in the input stream of notes. It discards short notes (noise) and impose a maximum temporal length on a sequence. The output is a list of tones (float number).

### Sequence matcher

The sequence matcher is the last piece of this program, it receive sequences of notes, try to match the command that correspond to it and if a match is found, launch the command.

## Using this program

To use this program, you need to install sndpeek and then do :

	# getting the packet and building it
	go get github.com/iwky911/whistlelauncher
	# managing commands and their notes sequence
	bin/whistlelauncher config -c=<config file>
	# launching the program
	bin/whistlelauncher -c=<config file>


Have fun !

[ibm]: http://www.ibm.com/developerworks/library/os-whistle/index.html?ca=dgr-lnxw97whistlework
[sndpeek]: http://soundlab.cs.princeton.edu/software/sndpeek/