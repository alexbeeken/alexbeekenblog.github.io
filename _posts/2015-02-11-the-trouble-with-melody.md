<br>

# The Trouble with Melody

----------

Over the last few weeks, I've been looking at how one would generate a melody out of thin air. It turns out, rhythm and melody are very different and are comprised of dramatically different things.

I thought about how I could break down the process of writing music. You may hear a lot of composers talk about divine inspiration, emotional inspiration, self expression or just trying to show off how good they are. They sit at their piano and they just write it. It just comes out of nowhere. It comes from a book they read. It comes from a memory they have of their mother.

Melodies are human things and are difficult to break down for a computer. The computer doesn't have a sense of how something feels or sounds. You could say it's like asking a person who has been deaf their whole life to write you a song. However, that would actually be easier. That person may tend to write notes that have a visually pleasing motion. They have probably seen music notation or understand that songs have certain shapes to them when you look at them visually.

On the other hand, the only two decisions a computer can make is: picking the same option every time or picking at random. If you really want to get down to it, even the random function on a computer isn't considered truly random. That's not really in the scope of this topic though.

So how did I solve this? Well, I didn't but I got a few steps in the right direction.


# The First Step: Infinite Eighties

----------

You can check out the new pop hit that all the robots are listening to [on my github](http://github.com/alexbeeken/infinite_eighties).

This is a song that uses the rhythm randomizing techniques talked about in my last post. It sounds groovy, very fun to let it play for a few minutes.

However, as mentioned last time there is no melody. There is no theme, no real relationship between the different randomizations. This music is fun but
devoid of that continuity and completeness that even basic compositions have.



# The Second Step: The Cantus Firmus

----------

I was thinking about what to start with and I remembered the first week of music theory class when we talked about this thing called a Cantus Firmus. You can read all about it on [Open Music Theory](www.openmusictheory.com).

A cantus firmus is a series of whole notes that composers use as a basis for their compositions. It follows very strict rules of part writing and if you follow all the rules you will be able to create something that sounds very consonant, or pleasing.

Now we can take that decisions based approach and apply it to a very established concept. So, I took a look at the rules of a cantus firmus. It seemed to fall into two categories. The "hard rules" and "soft rules". The hard rules are things that will always sound bad or be incompatible when trying to write out a composition.

In general a cantus firmus cannot have any dissonant intervals, cannot outline any jazzy seventh chords, cannot go particularly high or low, cannot step or leap too much in one direction and it has to start/end in pretty much the same way every time.

The "hard rules" were easy enough to translate to code, so I began writing and I got a working version of that up within a few days.

# The Third Step: Designing A Music Writer

----------

In the first few versions of the program I tried a lot of different things. At first it was just about getting it to spit out the correct amount of notes. Then it was about getting it to take away notes that were dissonant. From there I added more checks for rules until it outputted something that was consistent with a few examples that I did on paper. It followed all of the hard rules.

At first, I was accounting for the fact that we may be working with different keys. In midi numbers, 60 is middle C. A lot of my tests used 60 as a base case. At some point I stop and realized that I didn't have to worry about the key. I could convert the phrase to whatever key it needed to be after it had been calculated. It made the whole process a lot simpler and outlined the fact that design was the hardest part about this whole adventure.

The first version I would consider worthy of mention uses 6 classes. `phrase`, `candidates`, `rules`, `scale`, `cantufirmus` and `note_converter`.

`phrase` held all the notes and kept track of the current state of the cantus firmus at any given time.

`candidates` was used to facilitate calculating the next note to add to phrase. It first asked `rules` for all the relationships that would break the rules. `candidates` passed those rules to `scale`, as strings, and `scale` gave back all the notes that had those relationships to phrase's last few notes. `candidates` then picked, at random from the remaining notes.

`cantusfirmus` facilitated everything.

`note_converter` took midi numbers as input and outputted a format that [VexFlow](http://www.vexflow.com/) could read. I was able to display the cantus firmus on a web page in this manner.

# The Fourth Step: Soft Rules

----------

I'll be honest this version outputted some ugly, incorrect and spastic musical phrases. I needed a way to police those soft rules that make the notes sound like they have some degree of intention.

The overall plan was to create an `evaluator` class that took a phrase as input and outputted a score based on a number of factors. I would use this with a bunch of real world examples to help guide the process of picking the next note. How it would guide the process.

Instead of picking at random from the remaining notes, `candidates` would ask the `evaluator` class to score each note that passed the "hard rules" checks. It would pick the top two and choose randomly among those.

Given a length of 8, this would give us one of 512 possible cantus firmi whenever we ran the algorithm.

I haven't actually gotten this working yet. I began to add this feature and then became bogged down in trying to redesign the whole structure of the program. So, as far as actually seeing this will give better results, I don't know yet. I do know that if it does, I'll likely extend this feature in the following way.

Instead of just getting one of the 512 or greater possible cantus firmi, we would generate all of them procedurally. From there, we would give each one a score and pick the top ten. Those top ten would then be added to our "real world" examples and be used by the `evaluator` to score the next round of cantus firmi. Inevitably this would lead to some interesting results, all of which would be valid for writing musical compositions.

# In Closing

----------

The last time this program was in working shape was after the third step. I haven't quite gotten it back to where I can show results yet for calculating soft rules.

The next few steps when this does happen is to add [first species counterpoint](http://openmusictheory.com/firstSpecies.html) on top of the generated cantus firmus using the exact same technique. The next step? Well, there are four counterpoint species. I'm thinking about them with each redesign, how to make it easier once I'm ready to add them in. So, stay tuned!


Thanks for visiting!

Check me out on twitter @BeekenAlex ! 
