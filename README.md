# uiflow

A compiler that creates UI flow graphs from markdown-style text.

## What are UI Flows?

When planning the user interfaces for things like websites or smartphone apps, a UI flow is a simple representation describing the entire flow of an application, like the one depicted below.

![simple.png](https://qiita-image-store.s3.amazonaws.com/0/35671/83a65590-8ea7-a124-0a86-ca9cb967402b.png "simple.png")

Without touching on a specific UI:

* Things the users see
* Things the users do

By observing these, we can simply describe the necessary conditions, and also the essentials, of a UI.

The ``uiflow`` compiler can construct concise representation diagrams like the following:

```markdown:sample.txt
[First]
Write the things the user sees
--
Write the things the user does

[Next]
Thing the user sees
--
The first thing the user does
==> The result from the first thing being done
The second thing the user does
==> The result from the second thing being done

[The result from the first thing being done]
Result

[The result from the second thing being done]
Result
```

## Installation

Graphviz is utilized in the creation of graphs. Using something like brew, please kindly install it.

```
brew install graphviz
npm install -g uiflow
```

## Command Usage

### Simple Usage

```bash:
uiflow -i myapp.txt -o myapp.png -f png  
```

Convert myapp.txt to png format and save it.

### Output as svg

```bash:
uiflow -i myapp.txt -f svg
```

Output myapp.txt in svg form to standard output.

## Grammar for the uiflow format

### Basic Blocks

```markdown:basic_blocks
[Page Name]
Display Element 1
Display Element 2
--
Action Element 1
===> Name of page after transition 1
Action Element 2
===> Name of page after transition 2
```


![base.png](https://qiita-image-store.s3.amazonaws.com/0/35671/f2b4855d-a53c-5414-50f1-de4f07bc2f16.png "base.png")

### Connecting Basic Blocks


```markdown:basic_blocks
[Page Name]
Display Element 1
--
Action Element 1

[Page Name 2]
Display Element
--
Action Element

[Page Name 3]
Display Element
--
Action Element
```
By omitting action elements' ``==>``, they are automatically connected to the next basic block.

![base.png](https://qiita-image-store.s3.amazonaws.com/0/35671/01f02911-6738-549f-dd93-b829887843fb.png "base.png")


### Naming Transition
By writing ``=={hogehoge}=>``, you can apply a name to the transition.

```markdown:basic_blocks
[First]
Write something the user sees
--
Write something the user does

[Next]
Thing the user sees
--
Thing the user does 1
=={Transition action name}=> Result 1
Thing the user does 2
=={Something like the name of an API}=> Result 2

[Result 1]
Result

[Result 2]
Result

```
![simple-graph.png](https://qiita-image-store.s3.amazonaws.com/0/35671/eff43bff-b436-c9e5-815e-559143cec750.png "simple-graph.png")


### A somewhat complicated example

```markdown:complex

[First Time Notification]
The notification's confirmation dialog
--
OK

[Country Selection]
Choices for a region
Japanese as default
OK button
--
OK

[Let's Begin]
Hint information
Start button
--
Swipe the hint
==> Let's Begin
Start LINEQ

[Main non-logged in screen]
Alert that you can use all features once logged in
Feed
Notification icon
Search icon
Home
Categories
Q button
Ranking
My Page (N)
------
Tap the alert
==> Terms of Use
Tap categories
==> Categories
Tap the Q button
==> Login screen
Tap ranking
==> Ranking


[Terms of Use]
Display of Terms of Use

[Categories]
Category details

[Ranking]
Ranking details

```
![base.png](https://qiita-image-store.s3.amazonaws.com/0/35671/5f26cd34-39bf-4dd3-c2b9-2ac892abbdd8.png "base.png")


## Using uiflow as a library

You can use it as follows:

```javascript
var uiflow = require("uiflow");

// Write out in dot form
var dot = uiflow.compile("[Test]\nThing the user sees\nThing the user does\n");

// Write out a parsed AST representation as JSON
var json = uiflow.json("[Test]\nThing the user sees\nThing the user does\n");

// Using graphviz, put an svg-converted version to standard output
uiflow.build("Source code","svg")
	.pipe(process.stdout);
```

## SEE ALSO

* [画面遷移に疑問を感じたあなたにオススメするUI Flowsというツール](http://www.standardinc.jp/reflection/article/ui-flows/)
* [A shorthand for designing UI flows](https://signalvnoise.com/posts/1926-a-shorthand-for-designing-ui-flows)
