# jtils-all.suite

jtils-all is Jerry's Full Utility and Custom Sensetalk Tooling Set, Eggplant Toolkit (Established Since 2020)

Add jtils-all.suite as a helper suite to any project to be able to utilize all of the useful tooling of the Sensetalk scripting language to assist with streamlining Eggplant scripting tasks and workflows.

> NOTE:
>
> New image collections should be taken and added to this suite such as each browser's refresh button on the toolbar as just one example.
>
> Scripts or handlers that are specific to authentication, browser, and OS may or may not work in your environment depending on IT and network configurations, versions, or some other variable.

## Some key features of the toolkit:

### *key.script*
- - - 
With *key.script*, there is no more need for `typeText`.  Calling into the key script is a cleaner way of coding any key press or key sequence.

Instead of the following:
```declarative
typeText controlKey,altKey,deleteKey
typeText shiftKey,tabKey
typeText altKey,tabKey
```

Call into the *key.script* as follows:
```declarative
key.ctrlAltDel
key.tabback
key.previousWindow
```


### *keys.script*
- - - 

Whereas *key.script* has predefined key presses or key sequences, *keys.script* accepts a string or series of strings and also looks for remoteWorkInterval() as the first parameter and is compatible with wait times.

Instead of the following:

`typeText "first name",tabKey,"last name",tabKey,returnKey`

Call into the *keys.script* as follows:

`keys "first name",tabKey,"last name",tabKey,returnKey`

But what if the script fails at times because of network bandwidth issues or server response issues and waits and remoteWorkInterval() settings need to be incorporated?

Standard Sensetalk implementation would require this block of code:
```declarative
set the remoteWorkInterval to 0.3
typeText "first name",tabKey
wait 1
typeText "last name",tabKey
wait 2
typeText returnKey
```

With jtils, the same block of code can be scripted as:
```declarative
keys 0.3,"first name",tabKey,wait 1,"last name",tabKey,wait 2,returnKey
```

### *pL.script*
- - - 

Script Sensetalk just for a bit, and it almost immediately gets annoying and time-consuming having to type out `imageName:`, `text:`, `searchRectangle:`, `waitFor:`, and all of the other text properties every single time anything needs to be done with either an image or OCR-based detection.

Constructs and returns a property list (pL) for use in image or text recognition/search operations. It dynamically determines if the source is an image or text string, then applies optional search constraints, wait times, and text ocr modifiers to the property list as specified.

Instead of this:
```declarative
set xy to imageLocation(text:"Last Name", searchRectangle: [0,0,960,540], waitFor: 10, invertImage: true, caseSensitive: true))
click image:"os/win/start", searchRectangle: [0,540,960,1080], waitFor: 10)
if imageFound(text:"B13888", searchRectangle: searchRectangle(), waitFor: 10, validCharacters: "*") then click foundImageLocation()
```

With the *pL.script*, the examples above can be written as follows:
```declarative
set xy to imageLocation(pL("Last Name", screenTL, invert, case, wait10))
click pL("os/win/start", screenBL, wait10)
if imageFound(pL("B13888", screenParent, wait10, validCharacters)) then click foundImageLocation()
```
You can also abbreviate further:

```declarative
set xy to imageLocation(pL("Last Name", tl, invert, case, 10))
click pL("os/win/start", bl, 10)
if imageFound(pL("B13888", parent, chars, 10)) then click foundImageLocation()
```

Even for the simplest query, instead of this:
```declarative
click image:"btnStart", waitFor: 10
```

*pL.script* is still a cleaner option:
```declarative
click pL("btnStart",10)
```

### *sr.script*
- - - 

The search rectangle (sr) script is used to define the searchRectangle based on remoteScreenSize().

The *sr.script* can be used to...

1. Call one of the predefined areas of the screen listed below
2. Pass in a percentage between 0..1 (i.e., 0.7)
3. Pass in imageLocation to anchor off of as any position of the rectangle
4. Search within specified radius from mouse cursor location or directionally from mouse cursor location or from a passed in coordinate/rectangle

Calling this handler without any parameters will clear any custom region or stale yellow rectangle lines on the SUT and reset the searchRectangle
back to fullscreen.

The *sr.script* is designed to work in-line with existing Sensetalk syntax.

The *sr.script* is called by many other parts of this toolkit and therefore should be deemed essential.

Here is the list of pre-defined screen regions:

|Region|          Description          |
|:-------------:|:-----------------------------:|
|t|           top edge            |
|tt|       top edge tighter        |
|ttt|       top edge tightest       |
|l|           left edge           |
|ll|       left edge tighter       |
|lll|      left edge tightest       |
|b|          bottom edge          |
|bb|      bottom edge tighter      |
|bbb|     bottom edge tightest      |
|r|          right edge           |
|rr|      right edge tighter       |
|rrr|      right edge tightest      |
|c|            center             |
|cc|        center tighter         |
|ccc|        center tightest        |
|ct|      center to top edge       |
|cl|      center to left edge      |
|cb|     center to bottom edge     |
|cr|     center to right edge      |
|tl|           top left            |
|tll|       top left tighter        |
|tlll|       top left tightest       |
|tr|           top right           |
|trr|       top right tighter       |
|trrr|      top right tightest       |
|br|         bottom right          |
|brr|     bottom right tighter      |
|brrr|     bottom right tightest     |
|bl|          bottom left          |
|bll|      bottom left tighter      |
|blll|     bottom left tightest      |
|m|            middle             |
|mm|        middle tighter         |
|mmm|        middle tightest        |
|tm|  top middle (sides omitted)   |
|tmm|      top middle tighter       |
|tmmm|      top middle tightest      |
|bm| bottom middle (sides omitted) |
|bmm|     bottom middle tighter     |
|bmmm|    bottom middle tightest     |

Various ways that the *sr.script* can be utilized:
```declarative
sr
sr(t)
sr(0,0,0.75,1)
sr set,tl
sr(radius,br,150)
sr(380, imageLocation("titleBarIcon").y, 1, imageLocation(text:"Legal Notice").y)
```

Current implementation of Eggplant Sensetalk where the searchRectangle needs to be defined and set back to fullscreen might look something like this:
```declarative
set the searchRectangle to [576,324,1344,756]
click text:variable1
set the searchRectangle  to []
click btnOK
```

Utilizing *sr.script*, the same block of code would be scripted as follows:
```declarative
sr set,screenCC
click text:variable1
sr
click btnOK
```

### *waitUntil.script*
- - - 
The *waitUntil.script* waits until an image or text is found or until an image or text is no longer on the screen.

A problem with Eggplant's built-in "wait until" function is that the execution can continue to the next line of code too fast and in some instances, this can cause a failure.  There is also no way to wait after.

The *waitUntil.script* can wait a specified time after the main function as well as add far more informative logging for troubleshooting/debugging purposes.

Expected Parameters:
```declarative
1. duration: number (default: 9.99 seconds)
2. imageOrText: image filepath/filename or text string
3. notFound: "found" or "not found"  (default: found)
4. waitAfter: should begin with 'wait' followed by number (default: 0 (no waiting after))
5. searchRec: compatible with sr.script (default: not specified)
6. dpi: should begin with 'dpi' followed by number (default: 85)
```

Usage Syntax:
```declarative
waitUntil 10,"browser/refresh",found
waitUntil 30,"Save Print Output As",notFound,wait10
waitUntil 60,"Loading",notFound,wait1,sr(c),dpi144
waitUntil 120,"Finished processing",found,wait0,[100,100,1000,1000],dpi72
```

- - -

Many, many more uses for these scripts as well as the other scripts and handlers within this toolkit.  Please review the notes at the beginning of each script and/or handler for details, explanations, and usage syntax.

Make sure that no other script has the same name as any of the scripts in this toolkit.  Code will not look as clean if you have to type out a path into these scripts.  That would defeat the purpose of lessening the amount of typing which is one of the main benefits and advantage with this Sensetalk custom tooling and why it is designed in the way that it is.

Alleviating the frustration of having to type the same things out over and over again is a huge advantage over the standard "out of the box" Sensetalk syntax.


>
> Eggplant Sensetalk Toolkit (Established Since 2020)
>
> Author Name:  Jerry Park
>
