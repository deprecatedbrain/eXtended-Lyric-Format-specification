# eXtended Lyric Format (xlyr)
Version: 1.0 (hybrid XML spec)
Standard extension: `.xlyr`
MIME type: `application/xlyr+xml`

## 1. Introduction

The eXtended Lyric Format (.xlyr) is a hybrid XML/Markup specification designed to synchronize text lyrics with audio playback. It combines the established, rigorous hierarchy of XML (for structure, track, metadata, etc) with an inline, human-legible timestamp syntax (for syllable-level timing and dynamics).

## 2. Document Structure/hierarchy

```
<song> (Root)
 - <metadata> (Global Song Info)
 - <structure> (Arrangement Sections)
      - <section> (Verse, Chorus, etc.)
           - <line> (Sequential Lyrics)
           - <timeline> (Simultaneous Layers)
                - <track> (Individual Singer Stream)
                     - <line>
```

## 3. Element/Attribute spec
### 3.1 `<song>` - Root element

Outermost wrapper for entire file. Has no attributes.

### 3.2 `<metadata>` - Global tags for describing the audio
 **Allowed child tags:**

* `<title>` (string, required)
* `<artist>` (string, required)
* `<bpm>` (integer, optional)
* `<key>` (string, optional) - follows normal musical syntax (e.g. "Em", "F#maj")

### 3.3 `<structure>` - Parent container for progression of the song.
**Allowed child tags:**

* `<section>`

### 3.4 `<section>` - Wrapper for specific musical blocks (e.g. verse, chorus, bridge, intro, instrumental)

Attributes:
 * `type` (string, required): Must be one of the following *(see footnote 1)* `Intro`, `Verse`, `Pre-Chorus`, `Chorus`, `Bridge`, `Outro`, `Instrumental`.
 * `number` (integer, optional): Integer modifier (e.g. `number=1` for the first verse/Verse 1)
 * `label` (string, optional): Additional custom display string (e.g., `label="Guitar Solo"`)
 
### 3.5 `<timeline>`
Used only when vocals overlap. Acts as a multi-track grid allowing for multiple `<track>` streams to execute concurrently using identical timestamp rules.

### 3.6 `<track>`
Represents a single vocal layer inside a `<timeline>`

Attributes:
* `voice` (required, string): Name/identifier of the singer. Must be unique.
* `role` (reqired, string): Must be `lead`, `backing`, `group`, or `spoken`

### 3.7 `<line>`
The base element that contains the timed lyric text. Can live directly under a `<section>` for single, sequential singing, or under a `<track>` when vocals overlap.

Attributes:
* `voice` (optional under track - required if direct child of sections, string): Name/identifier of the singer. Must be unique
* `style` (optional): Vocal dynamic delivery indicators. Recommended tokens: `soft`, `belt`, `crec`, `whisper`, `spoken`, `falsetto`.

## 4. Inline Micro-Syntax Rules
The text inside a `<line>` tag must be parsed using the following regular expressions and syntax rules for timing, syllables, and behaviors:

table here with timestamp marker, systain marker, rest/pause, syllable break

## 5. Complete Implementation Reference Example
```xml
<?xml version="1.0" encoding="UTF-8"?>
<song>
  <metadata>
    <title>Silly Singers</title>
    <artist>The Super People</artist>
    <bpm>128</bpm>
    <key>Em</key>
  </metadata>

  <structure>
    <section type="Intro">
      <line voice="System" style="spoken">
        <00:00.00>[Instrumental]
      </line>
    </section>

    <section type="Verse" number="1">
      <line voice="John" style="soft">
        <00:15.00>Walk<00:15.20>ing <00:15.40>the <00:15.60>walk~ 
        <00:16.00>talk<00:16.20>ing <00:16.40>to <00:16.60>a <00:16.80>pud<00:17.00>ding <00:17.20>cup.
      </line>
      <line voice="John" style="belt">
        <00:18.50>Why <00:18.80>is <00:19.10>my <00:19.40>en<00:19.70>tire <00:20.00>sock <00:20.30>dra<00:20.60>wer <00:21.00>fill<00:21.20>ed <00:21.40>with <00:21.60>soup?
      </line>
    </section>

    <section type="Pre-Chorus">
      <line voice="Sarah" style="crec">
        <00:22.00>The <00:22.30>ca<00:22.60>t <00:22.90>is <00:23.20>fly<00:23.50>ing <00:23.80>past <00:24.10>the <00:24.40>moon~
        <00:25.00>it <00:25.30>has <00:25.60>a <00:25.90>mon<00:26.20>key <00:26.50>on <00:26.80>its <00:27.10>back!
      </line>
    </section>

    <section type="Chorus">
      <timeline>
        <track voice="John" role="lead">
          <line style="belt">
            <00:28.00>Oh <00:28.30>dan<00:28.60>cing <00:28.90>ba<00:29.20>na<00:29.50>nas <00:29.80>in <00:30.10>the <00:30.40>sky!
            <00:31.00>Don't <00:31.30>ask <00:31.60>me <00:31.90>how <00:32.20>they <00:32.50>learn<00:32.80>ed <00:33.10>to <00:33.40>fly!
          </line>
        </track>
        <track voice="Sarah" role="backing">
          <line style="falsetto">
            <00:28.00>Yel<00:28.50>low <00:29.00>fruit! <00:29.50>Yel<00:30.00>low <00:30.50>fruit!
            <00:31.00>Fly<00:31.50>ing <00:32.00>high! <00:32.50>So <00:33.00>high!
          </line>
        </track>
      </timeline>
    </section>

    <section type="Outro">
      <line voice="John" style="whisper">
        <00:35.00>The <00:35.40>pud<00:35.80>ding <00:36.20>did<00:36.60>n't <00:37.00>an<00:37.40>swer <00:37.80>back...
      </line>
      <line voice="System" style="spoken">
        <00:39.00>[Fade Out]
      </line>
    </section>
  </structure>
</song>
```

## X. Footnotes

1. Additional musical block types may be used, however the provided types are the only ones that are expected to work.
