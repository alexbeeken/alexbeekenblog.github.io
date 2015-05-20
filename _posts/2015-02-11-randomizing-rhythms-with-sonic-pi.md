<br>

# Randomizing and Automation

----------

When I was a teenager I remember someone on a Discovery channel documentary talking about the two components of creative AI algorithms at the time: A generator that produces random "static" with constraints and an evaluator that looks for patterns within the static.

When I heard about  Sonic Pi, an awesome music coding program that lets you compose music with Ruby code, I wanted to see what kind of randomness I could drum up with it. Random numbers are quite powerful. Efficiently creating them can be a big step toward automation. Is a bit of your soul lost in the process? I have no idea but I do know that sometimes these metallic cats can play some pretty funky beats.

Still, no AI robot music making the charts yet. Although, it's <a href="http://singularityhub.com/2014/05/27/software-composes-music-inspired-by-classic-novels/">getting better</a> all the time. With the help of Sonic Pi we can hopefully try to constrain the static to a sweet spot between random sound and old fashion <i>boring</i> human music.

If you haven't already closed this window, googled Sonic Pi and started playing with it, go ahead and download it free <a href="http://sonic-pi.net/">Here</a>.

In fact if it is your first time installing Sonic Pi, I highly recommend going through the built in tutorial. It is well written with lots of great examples to get you started.

If all this programming stuff is brand new to you, I recommend going through <a href="teamtreehouse.com">Team TreeHouse</a> to learn the basics of Ruby syntax before you get too confused.

<br>

# Subdivisions

----------
Let's get some terms defined and some information to help understand a little about how I'm approaching this.

The <b>beat</b> is the basis of any song's rhythm. A beat is considered to be the place where you would tap your foot or nod your head. It's the thump, the groove, that underlying clicking underneath the notes.  The beat in classical music is grouped into <b>measures</b> which can then determine things like emphasis and phrasing. The speed at which the beats are played is the <b>tempo</b>.

Classical music's concept of a beat is <b> arbitrary.</b> Historically composers would use words to describe how the beat feels instead of a number you can measure like beats per minute. You can base your tempo off of a measurement but this is generally more difficult than just <i>feeling</i> the music. This is because musical notes are divided up into <b>subdivisions</b>.  These subdivisions, used in different combinations, contain all possible rhythms (in theory) and can be played at any tempo you would like. You don't need to know exactly how long to play them, you just know how long you should play them in relation to each other.

We almost always choose the <b> quarter note </b> to be "worth" 1 beat. If you want a note to be held longer than a quarter note, you use a <b>half note</b>, worth 2, or a <b>whole note</b>. This means we want the length of a quarter note to be the same as the beat of the song. The names of rhythmic values can be confusing, don't worry about the meaning behind them right now.

If you divide a quarter note into two smaller beats. We call those <b> eighth notes </b>. Dividing <i> those </i>notes in half gives us two <b>sixteenth notes</b> and if you are lucky you may see <b>thirty second notes</b> and <b>sixty forth notes</b> in more difficult music.

Combinations of smaller rhythms that play off of a steady beat are <b>syncopations</b> and that's as far as I'll get to in this post in terms of music theory.

So , when we are creating random rhythms we need to tell the computer exactly how long we want to play each note for. We have to be very specific because software has no problem doing something the exact same way for as long as it is running.

If this is too confusing, a good place to get another explanation is <a href="http://www.musictheory.net">MusicTheory.net</a>.

<br>

# Sonic Pi

----------

The beat in Sonic Pi is determined by how long you `sleep` between notes.

In these examples we will treat 1 as the quarter note. To find the value of a half note you multiply 1 by 2. To find the subdivision for an eighth note you  divide 1 by 2. To find the subdivision for a sixteenth note you divide 0.5 by 2. Like so...

<img src="public/table.png">

To hear the different subdivisions we can put them inside a loop by calling `times` on an integer representing the number of times you want to play the note. We then `sample` the sound of a snare to hear the note and `sleep` the amount that corresponds to each note value.

<b>Quarter notes</b>

```
4.times do
  sample :drum_snare_soft
  sleep 1
end
```

<b>Eighth notes</b>

```
4.times do
  sample :drum_snare_soft
  sleep 0.5
end
```

<b>Sixteenth notes</b>

```
4.times do
  sample :drum_snare_soft
  sleep 0.25
end
```
<br>

# Jazzing it Up!

----------

Okay now to the fun stuff.

This basic approach to randomizing relies on a one line algorithm that multiplies the smallest note value you want to be sampled. I call this rhythm it the <b>lowest rhythmic denominator</b>.

What I mean by lowest rhythmic denominator is the fastest possible rhythm you want when you start randomizing. You make this the smallest amount of time you tell the program to `sleep` after playing the note. If you simply multiply this amount of time by a random integer(whole number) generated by the `rand_i()` function, you will get a bunch of rhythmic syncopations.

Because we are inside of a continuous loop, the next note will execute as soon as the sleep time runs out from the last note. If `rand_i()` rolls a 0, the sleep time will be zero and we will hear two notes played at the same time. So we add `(rand_i() + 1)`  so the note value will never end up being zero and we will always have a sleep time that is greater than or equal to the lowest rhythmic denominator.

```
loop do
  sample :drum_snare_hard
  sleep 0.5 * (rand_i(4) + 1)
end
```

This results in a random note played with a random duration between an eighth note and a half note tied to an eighth note. It doesn't sound like much right now but it'll get better in the next few examples.

```
use_random_seed 1

loop do
  sample :drum_bass_hard
  sleep 0.5 * (1 + rand_i(4))
end
```

If you put `use_random_seed` above any loop that uses a random function you are using a <b>seed</b> to randomize on. A seed allows you to get the same result from the rand function the next time you run it. If you like a rhythm generated when you use a seed of 72, you can use that same number to seed it the next time you want to get that specific rhythm.

So, now that we have a random rhythm going, let’s see about playing that rhythm at the same time as a drum kick.

```
in_thread do
  loop do
    sample :drum_snare_hard
    sleep 0.125 * (1 + rand_i(8))
  end
end

in_thread do
  loop do
    sample :drum_bass_hard
    sleep 1
  end
end
```

With the bass kick  to mark time we now can feel those random syncopations with `sample :drum_bass_hard`. It ends up comes off like a restrained rock and roll drum solo.

In this code I added an `in_thread` wrapper to both loops. I have the bass kick on quarter notes here. This creates a kind of anchor for you to hear the syncopations of the snare drum. You can hear some pretty clever rhythms in this one starting to emerge. I encourage you to experiment with changing the values.

```
in_thread do
  loop do
    sample :drum_snare_hard
    sleep 0.125 * (1 + rand_i(8))
  end
end

in_thread do
  loop do
    sample :drum_bass_hard
    sleep 0.5
  end
end

in_thread do
  loop do
    sample :drum_cymbal_closed
    sleep 0.25
  end
end
```

In this code I added the `sample :drum_cymbal_closed` and had it sampling on sixteenth note subdivisions.

I changed the bass to play on eighth notes and the snare’s lowest rhythmic denominator to be 32nd notes. It gives this beat a driving feel and highlights how much just adding the cymbal fills out the sound here.

By making the bass kick on eighth notes instead of quarters, I’m essentially just doubling the speed. So we could just call the bass kick quarters and base all of our references to normal notation on that. However, I want to stay true to the subdivisions table above and only refer to note names in that table. This demonstrates how subjective classical notation can be. You would never write notation for a snare drum in 32nd notes unless you absolutely had to. However, in this case it just sounds good and the computer can handle that level of complexity no problem.

We can refine this simple outline a bit still to give us some more flexibility when we want to use it in our program.

```
in_thread do
  loop do
    sample :drum_snare_hard
    sleep 0.125 * (1 + rand_i(8))
  end
end

in_thread do
  loop do
    sample :drum_bass_hard
    sleep 0.5
  end
end

in_thread do
  loop do
    sample :drum_cymbal_closed
    sleep 0.125 * (1 + rand_i(4))
  end
end
```
In this code, I added a way to create a counter rhythm between the cymbal and snare. I randomized the cymbal with a shorter range with `rand_i(4)` instead of `rand_i(8)` like I did for the snare. This gives the cymbal a lot more small subdivision syncopations. My ear expects the cymbal to play more often than the snare so I thought it sounded more full this way.

On accident, I had set `use_random_seed` to the same number for both the cymbal and the snare. This was a great demonstration of how random seed works, it sounds like these two were “given the same music” to read from. You can see what I'm talking about by changing the seed to the same value for both instruments.

We're not quite done. The example above creates beats that are a little lost in terms of phrasing. There is no continuity or connection to one group of beats over any other. The algorithm just keeps playing on random sleep times with no sense of a complete song. So let’s add the capability to repeat a phrase.

```
in_thread do
  loop do
    sample :drum_bass_hard
    sleep 0.5
  end
end

in_thread do
use_random_seed 6
  loop do
    sample :drum_cymbal_closed
    sleep 0.25
  end
end

in_thread do
  loop do
  use_random_seed 5895
  phrase_length = 4
  sleep_time = 0
  end_loop = false
    until end_loop == true  do
    sleep_time = 0.125 * (1 + (rand_i(8)))
      if sleep_time < phrase_length
        phrase_length = (phrase_length - sleep_time)
        sleep (sleep_time.abs)
      else
        sleep phrase_length
        end_loop = true
      end
      sample :drum_snare_hard
      end
  end
end
```

So, here’s how I used a little bit of math to create a phrase that repeats over and over:

I keep track of how much time is needed to complete the phrase with a float(decimal number) called `time_left`. Each time through the loop, I subtract the current note value from time_left. Eventually, that note value is more than how much time is left in the phrase. When that happens, it sleeps the rest of that time and restarts the loop.

I hope you enjoyed this information and I hope it was helpful to you. There is probably an easier and more elegant way of doing this and I welcome any feedback or thoughts you have.

Wait... how do I get some sweet randomized melodies?

Well, you can change the `sample` lines in this code to `play 60` and find some cool rand_i uses with integers. You can also save the notes you like into an array like so [60, 55, 43, 52] and sample each one with .sample.

Confused? I'll be posting it about soon so stay tuned!

(Thanks to <a href="http://clem.ninja">Clem</a> for showing me how to make this website!!)
