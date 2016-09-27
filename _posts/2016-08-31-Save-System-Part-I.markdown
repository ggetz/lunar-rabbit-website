---
layout: post
title: "Heart Line Save System Part I"
date: 2016-06-28 12:12:12
categories: development
author: Colan Biemer
---

The first big decision for a save system is where and how you want to save the data. For Unity you have several options
and we'll focus mainly on ```PlayerPrefs```. An altenative to ```PlayerPrefs``` would be files that are saved
locally for use. These files could be serialized objects, JSON, XML, and more. Each of these have their advantages and 
disadvantages that can be gone over in a future blog post. Today we'll pretend like they don't exist.

For starters, why did we chooose to use ```PlayerPrefs```? One of the strengths of ```PlayerPrefs``` is how easy 
it is to use. For example say I want to save the current level a player is on:
```c#
PlayerPrefs.SetInt("PlayerLevel", level);
```

That's it. To retrieve it I can use:
```c#
int level = PlayerPrefs.GetInt("PlayerLevel");
```

Looking at the above code I notice an issue. We are using a magic string for retrieving the 
level everytime. So instead declare a string at the beginning of our class which handles saving.
```c#
const string LevelKey = "PlayerLevel";
```

I've used a constant because we are never going to change the variable ```LevelKey``` in our code. We can now
use this code to save:
```c#
PlayerPrefs.SetInt(GameBucket.LevelKey, level);
```

where ```GameBucket``` is the name of class which is saving the information. 

We now have a basic understanding of how to use ```PlayerPrefs```, but we've only saved an Integer. We can also
save other primitives, but not objects. This limitation can lead to poor design that will hamper progress
as development movess forward. To remedy this our group had a long conversation about how we wanted saving to 
work in the game. The most important part of the discussion was when we decided for there to be only one save rather 
than multiple. This meant ```PlayerPrefs``` was a solid option as we could use common sense keys without having to write
extra code to handle multiple saves. 

For Heartline, we have two things we want to save:
1. Level
2. Events ids

Saving the level is exceedingly easy as you can see from above. For events, though, we have a bit more work to do because
it will be in the form of an array which is not a primitive type. This means we are now required to set up our own form 
of an array. Look at this array:
```
[1,2,3,4,5]
```
We can see that it has a certain amount elemnts which we can iterate on. In ```PlayerPrefs``` it means we have to keep 
size of the array as well as the elements. The easiest way to do this is to save the size of the array as well as each 
element with a number associated with it. Our data ends up having the following form:
```
Key 		Value
Count		3
0 			"event id 1"
1 			"event id 5"
2 			"event id 8"
```

where the "event id 1" is a string representative of an ID which will create an event else where in the code. Our count 
gives us the size of the array to loop through. To loop we can use the following code to save:

```c#
// save array size
PlayerPrefs.SetInt(GameBucket.CountKey, events.Count);

// save keys by index
for (int i = 0; i < events.Count; ++i)
{
    // save to main save
    PlayerPrefs.SetString(i.ToString(), events[i]);
}
```

You can see that we call ```ToString()``` to turn the integer into a string to act as the key for the event id. Similarly
to load we can use:

```c#
int level = PlayerPrefs.GetInt(GameBucket.LevelKey);

List<string> events = new List<string>();

// loop through player prefs to get all the events
for (int i = 0; i < PlayerPrefs.GetInt(countKey, 0); ++i)
{
    // Add event to events list, by grabbing it with the coressponding index that 
    // has been turned into a string.
    string newEvent = PlayerPrefs.GetString(i.ToString());
    events.Add(newEvent);
}
```

We now have a save system that can save and load the level as well as the events . In the next post we'll
go over backups and how to handle hanging memory in ```PlayerPrefs``` with this sytem. 
