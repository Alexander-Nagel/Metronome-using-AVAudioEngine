# Metronome-using-AVAudioEngine

This is a simple sample-accurate metronome using two sounds (beat 1 versus beats 2+3+4) using AVAudioEngine. 

It displays the current beat as visual feedback in perfect sync to click sounds.

After many resultless experiments I finally got this working using only AVAudioEngine - without divin' down to Audiounit levels :-) 

Feel free to do whatever you like with this template. 

There's a Stackoverflow thread about this here: 

https://stackoverflow.com/questions/67341908/timing-issues-metronome-using-avaudioengine-schedulebuffers-completion-handler

... just in case you're interested in my previous apporach that did NOT work as well as this:-) 

Many thank to Bob McCune, the creator of this slide: 

https://www.slideshare.net/bobmccune/building-modern-audio-apps-with-avaudioengine

This finally helped my visualize the whole concept of scheduling buffers with AVAudioEngine.

The most important lesson I learned: The completionHandler callback provided by the scheduleBuffer command is not called early enough to trigger re-scheduling of another buffer while the first one is still playing. This will result in (inaudible) gaps between the sounds and mess up the timing. 
There must already be another buffer "in reserve", i.e. having been schdeduled before the current one has been scheduled. 

Using the completionCallbackType parameter of scheduleBuffer didn't change much considering the time of the completion callback: 
When setting it to .dataRendered or .dataConsumed the callback was already to late to re-schedule another buffer. Using .dataPlayedback made things only worse :-) 

So, to achieve seamless playback (with correct timing!) I simply activated a timer that triggers twice per period. All odd numbered timer events will re-schedule another buffer. Sometimes the solution is easy. But sometimes you have to try almost every wrong approach first to find it ;-) 
