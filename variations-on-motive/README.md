# variations-on-motive
Given a motive, programmatically generate prescribed variations:

1/2-1/8 durations are the only valid durations

Variations require the following operations:
1. Rhythmic shift: is reordering and possibly changing durations
1. melodic shift: is pitch shift. 
    * now we demonstrate 1 note melodic shift:
        * in key of Eb
            * given these pitches: `Eb4, F4, G4, Bb4, C5`
            * result in these pitches, when unconstrained: `F4, G4, Bb4, C5, Eb5`
            * for the NEA Create, we need to constrain instead. resulting in: `F4, G4, Bb4, C5, Eb4`
                * **ALGORITHM IS SO DUMB, DOESN'T NEED TO KNOW ABOUT ORDER AND CHANGING OCTAVES, JUST REORDER EXISTING PITCHES OR DURATIONS**
    * now we demonstrate 2 note melodic shift:
        * in key of Eb
            * given these pitches: `Eb4, F4, G4, Bb4, C5`
            * for the NEA Create, we need to constrain instead. resulting in: `G4, Bb4, C5, Eb4, F4`
    * now we demonstrate 3 note melodic shift:
        * in key of Eb
            * given these pitches: `Eb4, F4, G4, Bb4, C5`
            * for the NEA Create, we need to constrain instead. resulting in: `Bb4, C5, Eb4, F4, G4`

* motive and its variations should have only notes within a single "chord-scale-bucket":
    * tonic
        * pitches depend on the key 🤯?!?!?!?? (but the numbers "scale-degrees" - NOT ROMAN NUMERALS -  don't and i already have the numbers in the keynote `1      2     3     5     6`)
            * E flat == Eb:
                * pitches: `Eb, F, G, Bb, C`
            * F
                * pitches: `F, G, A, C, D`
    * subdominant
      * scale degrees: `2     4     5     6     1`
        * depends on the key 🤯?!?!?!??
            * E flat == Eb:
                * pitches: `F, Ab, Bb, C, Eb`
    * dominant
        * scale degrees: `7     2     4     5     6`
        * depends on the key 🤯?!?!?!??
            * E flat == Eb:
                * pitches: `D, F, Ab, Bb, C`
* given a piece, say "Deep River", is the key fixed across all transpositions?
    * no

* only for tonic:
    * pick 1 octave:
        * no I refuse, because I'm a musician. rather I will choose 2 octaves, deal with it. they are 4 and 5:
            * list the pitches in increasing order:
                * `Eb4, F4, G4, Bb4, C5`
        * no I refuse, because I'm a musician. rather I will choose 2 octaves, deal with it. they are 3 and 4:
            * list the pitches in increasing order:
                * `Eb3, F3, G3, Bb3, C4`

```
chromatic_scale = ['C', ['C#','Db'], 'D', ['D#', 'Eb'], 'E', 'F', ['F#', 'Gb'], 'G', ['G#', 'Ab'], 'A', ['A#', 'Bb'] 'B']
key eb pitches for tonic: `Eb, F, G, Bb, C`
say we want tonic for Ab: 
1. look below at tonic bucket intervals, and because the signature is "Ab" and bc the 1st interval in tonic_bucket_interals is 0, we know that Ab is the first pitch in the tonic scale degree for Ab.
1. next we see that the 2nd pitch is has an interval of 2. so we go from the previous note (Ab) up 2 semitones and get Bb

so given the flat.io score for the melody for the piece for this student's instrument's transposition, get its: ['score-partwise'].part[0].measure[0].attributes[0].key.fifths which for DBTRS-Melody for the Bb instruments in MusicCPR we will get the DBTRS-Melody-Bb and it will have the fifths value of -2 so this is an offset measured in semitones from C. counting back 2 in my ordered list of 12 semitones from C I end up at Bb. therefore Bb is the reference "step"/semitone for the scale degree offsets below. 

// Dictionary to map semitones to diatonic intervals
const diatonicIntervals = {
  0: 0,   // Unison
  1: 2,   // Major 2nd
  2: 4,   // Major 3rd
  3: 5,   // Perfect 4th
  4: 7,   // Perfect 5th
  5: 9,   // Major 6th
  6: 11   // Major 7th
};


//unit tests
//f2040 is in Eb (which is maybe simpler for the code bc michael plans to store the scale degrees in Eb)
// simple: F2040, student who plays the flute (C instrument) which is Concert Pitch TC

// complex: F2040, student who plays trumpet (Bb instrument) Bb in title
// french horn ("F Horn" in Musiccpr) is more difficult (F instrument and title) F in title

// down by the riverside
// simpler is flute
// more complex is trumpet
// french horn is more difficult
tonic_bucket_intervals = {
        {'name':'1','offset': 0},   // Unison
        {'name':'2','offset': 2},   // Major 2nd
        {'name':'3','offset': 4},   // Major 3rd
        {'name':'5','offset': 7},   // Perfect 5th
        {'name':'6','offset': 9},   // Major 6th
}

subdominant_bucket_Intervals = {
        {'name':'1','offset': 0},   // Unison
        {'name':'2','offset': 2},   // Major 2nd
        {'name':'4','offset': 5},   // Perfect 4th
        {'name':'5','offset': 7},   // Perfect 5th
        {'name':'6','offset': 9},   // Major 6th
}

dominant_bucket_intervals = {
        {'name':'2','offset': 2},   // Major 2nd
        {'name':'4','offset': 5},   // Perfect 4th
        {'name':'5','offset': 7},   // Perfect 5th
        {'name':'6','offset': 9},   // Major 6th
        {'name':'7','offset': 11}   # // 7th
}

const EbScaleDegrees = [Eb, F, G, Bb, C];

//follow the diatonicInterval top convert Eb to F

const FScaleDegrees = [ ]

// to go e.g. from Eb to F keyChange = 1
// Transpose each note diatonically and constrain to a single octave
  for (const track of midi.tracks) {
    for (const note of track.notes) {
      if (note.midi in diatonicIntervals) {
        const transposedNote = note.midi + diatonicIntervals[keyChange];
        note.midi = Math.min(Math.max(transposedNote, 0), 11); // Constrain to a single octave
      }
    }
  }


{
  "staff": "1",
  "voice": "1",
  "duration": "2",
  "pitch": {
    "octave": "4",
    "step": "E",
    "alter": "-1"
  },
  "$adagio-location": {
    "timePos": 0
  },
  "type": "quarter"
},


as midi number 0-127 (octaves: -1, 9) = 63


midi 0 is C-1 --> {
  "pitch": {
    "octave": "-1",
    "step": "C",
  },
} + 1 midi value
{
  "pitch": {
    "octave": "-1",
    "step": "D",

    "alter": "-1"
  },
}



measure0Pitches: [Eb, F, G, C, Bb]



def midi_to_musicxml_pitch(midi_note_number):
    # List of note names for white keys
    note_names = ['C', 'D', 'E', 'F', 'G', 'A', 'B']

    # Determine the octave and the note name
    octave = (midi_note_number // 12) - 1
    note_index = midi_note_number % 12
    note_name = note_names[note_index % 7]

    # Determine if the note is a sharp or flat (enharmonic equivalent)
    if note_name in ['C', 'D', 'F', 'G', 'A']:
        # Use sharps for these note names
        accidental = '#' if note_index % 7 in [1, 3, 6, 8, 10] else ''
    else:
        # Use flats for other note names
        accidental = 'b' if note_index % 7 in [2, 4, 5, 7, 9, 11] else ''

    return f"{note_name}{accidental}{octave}"

# Usage example:
midi_note_number = 60  # MIDI note number for C4
musicxml_pitch = midi_to_musicxml_pitch(midi_note_number)
print(f"MIDI Note Number: {midi_note_number}, MusicXML Pitch: {musicxml_pitch}")



def pitch_alteration(key_signature, note_index):
    # Dictionary to map the note index to the corresponding alteration (sharp or flat)
    key_accidentals = {
        'C': 0, 'G': 1, 'D': 2, 'A': 3, 'E': 4, 'B': 5, 'F#': 6, 'C#': 7,
        'F': -1, 'Bb': -2, 'Eb': -3, 'Ab': -4, 'Db': -5, 'Gb': -6, 'Cb': -7
    }

    # Get the note name from the note index
    note_name = ['C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#', 'A', 'A#', 'B'][note_index % 12]

    # Calculate the alteration for the note based on the key signature
    alteration = key_accidentals.get(note_name, 0)

    # Adjust the alteration based on the key signature (flats or sharps)
    if key_signature.endswith('b'):
        alteration -= key_signature.count('b')
    elif key_signature.endswith('#'):
        alteration += key_signature.count('#')

    return alteration

def midi_to_musicxml_pitch(midi_note_number, key_signature):
    # List of note names for white keys
    note_names = ['C', 'D', 'E', 'F', 'G', 'A', 'B']

    # Determine the octave and the note name
    octave = (midi_note_number // 12) - 1
    note_index = midi_note_number % 12
    note_name = note_names[note_index % 7]

    # Determine the alteration for the note based on the key signature
    alteration = pitch_alteration(key_signature, note_index)

    # Adjust the note name based on the alteration
    note_name += '#' * alteration if alteration > 0 else 'b' * (-alteration)

    return f"{note_name}{octave}"




algorithm to increment musicxml pitch by 1 semitone:
given: 
  steps ['C', ['C#','Db'], 'D', ['D#', 'Eb'], 'E', 'F', ['F#', 'Gb'], 'G', ['G#', 'Ab'], 'A', ['A#', 'Bb'] 'B']
  const diatonicIntervals = {
  0: 0,   // Unison
  1: 2,   // Major 2nd
  2: 4,   // Major 3rd
  3: 5,   // Perfect 4th
  4: 7,   // Perfect 5th
  5: 9,   // Major 6th
  6: 11   // Major 7th
};

Eb4, F4, G4, Bb4, C5
Ab, Bb, C+, Eb, F

key eb pitches for tonic: `Eb4, F4, G4, Bb4, C5`
key f pitches for tonic: 
  1. first realize eb-f is a delta of 2 semitones (the intervale on the steps list above)
  1. apply the 2 semitone shift to these pitches: `Eb4, F4, G4, Bb4, C5` ==> `F4, G4, A4, C5, D5`


keys: eb, f, bb, 

QUESTION: is it the case that if i know that the student using musiccpr uses a particular transposition from the following list, i will always know whether to show that student C# or Db?
F
Eb
Concert Pitch TC 8va
Concert Pitch TC
Concert Pitch BC 8vb
Concert Pitch BC
Bb
Alto Clef

ANSWER: NO. It is the case that certain transpositions may more commonly use one rep than the other (e.g. C# rather than Db), but it's also the case that this can differ by piece of repertoire (so therefore at this level of experience students are already used to reading both.)


1. let's say we store the scale degrees in 3  flat.io scores all in Eb (which is a key signature, to match the keynote illustration)
1. we may need to know those scale degrees in a different key signature
    * what are all the key signatures that musiccpr cares about, so for NOW that means only the NEA create pieces?
        * ans: Eb, Bb, F, Ab, A-major (only for the favorite, and it doesn't transpose)
1. to go to another key signature like F, above we have the ordered list of pitches in an octave and we calculate the distance from Eb to the target key signature, then we add that distance to the Eb scale degree
1. in order to now whether to display sharps or flats, we look at the flat.io melody score for the target transposition and this will tell me the answer:
    * how? 


1. piece: Deep River which is in Eb
    * btw, i will already have the flat io score for the melody in the 8 "transpositions"
1. we already have the scale degrees in Eb
1. given 2 students:
    * A plays the piccolo (which is "concert pitch" according to ben's band chart, and it is "Concert Pitch TC 8va" in musiccpr "transposition")
        * what pitches define each of the 3 chord scale buckets for this piccolo player for the song deep river?
            * tonic: `Eb4, F4, G4, Bb4, C5`
            * subdominant: `F4, Ab4, Bb4, C5, Eb5`
            * dominant: `D4, F4, Ab4, Bb4, C5`
            * NOTE: for the piccolo specifically, th octaves should be 5-6, not 4-5
    * B plays the trumpet (which is Bb in both musiccpr tranpsotion and ben's chart)
        * what pitches define each of the 3 chord scale buckets for this trumpet player for the song deep river?
            * tonic: `F4, G4, A4, C5, D5`
            * subdominant: `G4, Bb4, C5, D5, F5`
            * dominant: `E4, G4, Bb4, C5, D5`
            * NOTE: the octaves here are fine for trumpet
1. down by the riverside is in Ab
1. we already have the scale degrees in Eb
    * distance from Eb to Ab is 5 semitones
        * Eb4, F4, G4, Bb4, C5 ==> Ab4, Bb4, C5, Eb5, F5 this is the tonic scale degrees in Ab
1. given 2 students:
    * A plays the piccolo (which is "concert pitch" according to ben's band chart, and it is "Concert Pitch TC 8va" in musiccpr "transposition")
        * what pitches define each of the 3 chord scale buckets for this piccolo player for the song down by the riverside?
            * tonic: `Ab4, Bb4, C5, Eb5, F5`
            * subdominant: `Bb4, Db5, Eb5, F5, Ab5`
            * dominant: `G4, Bb4, Db5, Eb5, F5`
    * B plays the trumpet (which is Bb in both musiccpr tranpsotion and ben's chart)
        * what pitches define each of the 3 chord scale buckets for this trumpet player for the song down by the river:
        * starting from tonic in Eb (because that's our reference in storage), 
            * bc instrument is in Bb (i don't care about the piece at all), so go distance Eb to Bb which is 7 semitones up (or 5 down)
            i need to get to not Ab itself because this instrument is Bb, rather to Bb, so:
                * tonic: Eb4, F4, G4, Bb4, C5 ==> Bb, C5, D5, F5, G5
                * subdominant: `C5, Eb5, F5, G5, Bb5`
                * dominant: `A4, C5, Eb5, F5, G5`
    * still need to know whether to show sharps or flats, so we will look at the flat.io score for the melody of this piece in thus student's key/transposition




*right now we have the "bug" (misconception) where all students (currently only compose in aural condition) start with the blank score in the same clef and key with no respect to their instrument or the corresponding piece
* ben: wants to make sure we can make a good octave decision


pitch:
  octave: 3,
  step: 'E'

pitch:
  octave: 3,
  step: 'F'

```