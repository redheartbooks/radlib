
Radlib Project
===

1. First make story work (http://www.redheartbooks.com/radlib/story)
2. Second, make words work (http://www.redheartbooks.com/radlib/story/:id/words/:label)
3. Third, make dictionary work (http://www.redheartbooks.com/radlib/dictionary/:topic/:domain)

Client should first be able to create a **story** = Story.new(:title = "", :source = "") to create an on-the-fly story from a source (written in Radlib Syntax).

Afterwards, **story** will contain an array of words that must be provided a **value**. Also, story will provide a new method, **html**, which represents the entire story rendered with either the supplied word, a reference to the supplied word, or an indication that the word must still be obtained.


Milestones
---

| Version Number | Expected Release Date | Description                               |   
| -------------: | :-------------------: | :---------------------------------------- |
| **v001**       | **2014-Sep-01**       | **unusable, still building core application** |
| v010           | 2014-Oct-01           | will fully support stories                |
| v020           | 2014-Nov-01           | will fully support words                  |
| v030           | 2014-Dec-01           | will fully support dictionaries           |


Version Log
---

| Version Number | Release Date | Description                                        |
| -------------: | -----------: | :------------------------------------------------- |
| **v001**       | 2014-09-01   | Initial version                                    |


Radlib Syntax
===

The first line of text is the Title (`radlib.title`)

- Each paragraph is denoted by two newlines (an empty line between paragraphs).

- Each sentence is on its own line.

- HTML is allowed as part of a text token, but should be avoided to keep seperate interests between the content and its display.

- Named labels, word types, and topics may include spaces.



Case matters! 
---

- In Radlib, names and types are case insensitive, but the case within the story is translated into a formatting command.  

- Use an initial upper case character to format the dynamic word into *Title Case* within the story.

- Use an initial lower case character to format the dynamic word as *lower case* within the story.


Story words - capturing word placeholders
---

Story words have three components, a *name*, a *type*, and a *topic*.  All but the *type* are optional. They are requested by using curly braces `{}`

- `{@name|type|topic}` will request a word of a given *type* for the given *topic* and associate it with *name*

- `{@name|type}` will request a word of a given *type* and associate it with *name*

- `{type|topic}` will request a word of a given *type* within the given *topic*, but cannot be referenced

- `{type}` will request a word of a given *type*, but cannot be referenced

Sometimes, you want to force the selection of dynamic words, but it is small enough to not be worth creating a topic. You may create a choice of dynamic words by supplying them explicitly. In doing so, you can reformat the result later in your story.

- `{@he|he,she}` will allow the selection of he or she.
- `{@He|He,She}` will allow the selection of He or She, and format the result using Title Case.

Later, as described below, one can reference the selection and transform it as such:

- `{{He|his,her}}` will transform the previous value of `@he` with similar positional values of *his* or *her*. So if the user had selected *She*, then the transformed reference would be *Her*.

This will work with any set of ordered values, so you could associate numbers with labels like in the following example:

```
	Pick a number from 1-3.
	You picked: {@number|1,2,3}
	
	{{number}} represents the color {{number|red,green,blue}}.
	
```

If the user chooses 2, then the story would read:


```
	Pick a number from 1-3.
	You picked: 2
	
	2 represents the color green.
	
```

To transform the result further, you can specify formatting types to references.

``` 
	{{Number|spell number}} represents the color {{number|red,green,blue}}.
```

For the chosen value of 2, this results in:

``` 
	Two represents the color green.
```


Referencing previously supplied story words
---

Previously captured words can be referenced to be used later in the story. 
You may replace the contents of a story word by using the `{{}}` reference operator.

- `{{name}}` will be replaced by the *lowercase version* of the word captured by `@name`.

- `{{Name}}` will be replaced by the *Title Case version* of the word captured by `@name`.

When a word capture was based upon a list (ie. `{@he|he,she}`, then the value can be further transformed by position, as in the following: `{{he|his,hers}}`. If the word is *he*, then the reference will be *his*. Similarly, if the word is *she*, then the reference will be *hers*.


Special references
---

- `{a}` will be replaced by *a* or *an* depending on whether the next word begins with an H or a vowel.

- `{name|spell number}` will attempt to convert *name* into a numeric vqlue, and then spell them in English.

- `{number|ordinal}` will attempt to convert *number* into a numeric value, and then convert it into an ordinal format, for example 1st, 2nd, or 3rd.


Word Types
---

- `adjective` is a word that describes a noun

- `adverb` is a word that desscribes a noun or verb

- `noun` is a person, place, thing, or idea

- `nouns` are a plural form of people, places, things, or ideas

- `proper noun` is a Capitalized noun used to denote a particular person, place, or thing.

- `verb` is an action

- `verbed` is an action in the past tense


Topics
---

Topics are an arbitrary method to filter the choices for a given dynamic word. Words can be attributed with mutliple topics. When a word is requested for a given topic and type, the known words will be filtered by words of the given type that are associated with the requested topic. This allows the story creator to partition non-exclusive groups of words as appropriate answers for dynamic word reqeusts.

A topic may be any arbitrary string, although convention suggests it is a lower case, space-separated label such as *animal aggression*.

Topics are always the last parameter in a story word capture command `{}`.


Words Syntax:
===

Words are defined by *type*.words files in the `/public/words` path. Each word is separated by a newline, but may include an optional number of topics separated by commas.

For example, you can create nouns by creating a file `/public/words/proper noun.words` with:

```
Andy: name
Carlson: name, location
James: name
Portland: location
The United States Government
Tim: name
United Sates: location
Washington: location, name
Wilsonville: location
	
```

Words are imported by default, assuming the type files are located in `/public/words`. If a type is requested, the appropriate file will be interrogated, using any specified topic filters. If no topic filter is specified, then all words will be available to fulfil the request. If there is no corresponding word type file, then any value will be considered appropriate.

Workflow
---

1. Create a new story
	- supply .title, .description, and .source (a Radlib syntax string)
2. Get a list of words that need to be provided
	- .words[] is an array of story words that need to be provided
	- .html is a string that renders the current state of story