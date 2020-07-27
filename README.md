# EasyRhythm for FMOD

EasyRhythm for FMOD is a solution to get you started on triggering Unity behaviour from FMOD events, particularly useful for games that have rhythmic elements or mechanics.

It includes two main features that will quickly allow you to get started without having to know how to code FMOD callbacks.

1. Triggering Behaviour based on the beat

This is useful for triggering anything from UI to character animations in time with the music.

2. Invoking Methods based on FMOD markers

This allows you to invoke a method based on an FMOD Destination marker. 
Any listener you make (A MonoBehaviour with IEasyListener implemented) that has a method with the same name as the marker will call it when the marker is passed.
This could be useful for:
- Triggering behaviour a few bars before the end of a music track
- Triggering behaviour periodically in a track, rather than on every beat/bar
- Triggering behaviour at a specific point in the track, for example at the chorus
- Triggering behvaiour from an audio event that doesn't conform to a strict tempo - eg. a classical piece of music,
- Triggering behaviour from sound FX - eg.you could sync an explosion's visuals to the audio instead of the other way around.

# Getting Started

This package includes an example scene and prefabs which demonstrate how to begin implementing EasyRhythm for FMOD. We recommend you start there and poke around through the code!

Here are some step by step instructions to get you started in your own scene.

Quick Tip: We recommend setting your audio to not be streaming, as streaming sometimes has lags that stop the audio from syncing properly.

# Quick Start

1. Drag the EasyRhythm Audio Manager prefab into your scene.

2. On the Audio Manager, locate an FMOD event (My Event Path). Don't forget to add your tempo, assign the event to a bank, and build (F7) in FMOD.

3. Create a class (must be a MonoBehaviour) for whatever object/s in the scene you want to control.

4. Add the interface 'IEasyListener' to that class and implement the OnBeat method:

<pre><code>public class ExampleClass : MonoBehaviour, IEasyListener
{
	public void OnBeat(EasyEvent audioEvent) 
	{ 
		// Do Something 
	}
}</pre></code>

Fill the body with whatever behaviour you want to happen every beat!

6. Drag your newly created class into the 'Listeners' field on the Audio Manager. 
You have to actually drag the COMPONENT that implements IEasyListener, NOT the full GameObject, otherwise it won't work.
To do this, lock the inspector with the Audio Manager, and att a new inspector window (right click on hierarchy > add tab > Inspector), then select your listener object
*Hoping to fix this in a future update!*

6.5 You can also add listeners individually - see AudioManagerExample for an example!

7. Enter play mode, then press the 'Start My Audio Event' button on the Audio Manager. Press Stop to stop!

# Invoking Methods Using Markers

1. Create a destination marker in your FMOD event, for example "Foo" (multiple words with single spaces are allowed, but capitilisation must be identical).

2. In your listener object (any object where you've implemented the IEasyListener interface), add a method with the same name as the FMOD marker. For example:

FMOD Destination marker: "Foo"

<pre><code>public class ExampleClass : MonoBehaviour, IEasyListener
{
	public void Foo(EasyEvent audioEvent)
	{
		// Do something
	}
}</pre></code>

3. Fill the body with whatever behaviour you want to happen every beat!

4. Hit play to see the magic happen!

# Extras

In both the OnBeat() method and custom methods based on markers, you have access to an EasyEvent - this is a wrapper class for the FMOD event instance.

With this, we've included some basic but useful info you can retrieve from the FMOD event:

<pre><code>public class ExampleClass : MonoBehaviour, IEasyListener
{
	public void OnBeat(EasyEvent audioEvent) 
	{ 
		audioEvent.start(); // Starts the audio event
		audioEvent.stop(); // stops the audio event
		audioEvent.EventName; // The name of the audio event that triggered this OnBeat
		audioEvent.CurrentBar;
		audioEvent.CurrentBeat;
		audioEvent.CurrentTempo;
		audioEvent.LastMarker; // The last FMOD marker that was passed
		audioEvent.LastMarkerPos; // the last FMOD marker's position in the timeline, as a float in seconds (eg. 5218 is 5.218 seconds)
		audioEvent.CurrentTimelinePosition; // The current timeline position, in seconds (similar to LastMarkerPos)
		audioEvent.TimeSigAsArray(); // Returns the current time signature as an array, where [0] is the top number and [1] is the bottom number
		audioEvent.TimeSigAsString(); // Return the current time signature as a string - eg. "4/4"
		audioEvent.BeatLength(); // Returns the length of a beat at the current tempo as a float, in seconds
	}
}</pre></code>

You could use this info to:
- Trigger behvariour only on beats 1 and 3 of a bar
- Trigger behaviour only on every 8th beat (by implementing your own counter)
- Start listening to the beat only after a certain amount of time has passed
- If using multiple of the same marker, check exactly which marker was just passed (useful for debugging)
- Determine animation speeds based on the tempo or beat length
- Check which event is playing
- Change behaviour based on time signatures
- Many more things!


