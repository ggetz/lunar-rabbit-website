---
layout: post
title: "Heartline Environment Procedural Generation Part II: Populating the World"
date: 2017-11-17 10:30:12
categories: development
author: Laura Mo
---

*How we added the various environment pieces to the world.*

Once the terrain has been generated, various environment pieces can be added to create an appealing looking background. In addition to our decision to use procedural generation to create the background layers, it was also decided early on for each layer to consist of a single color. Terrain and the environment pieces are all one solid color, allowing us to worry less about how objects might look when slightly overlapping each other or how they connect to the ground.

The end result is something like this:

![](http://lunarrabbit.com/img/posts/TerrainGenerationPost2/EnvironmentExample.png)

To place the environment assets onto the terrain, we first defined a series of rules for each asset that could be placed. We start with a pure white sprite asset like this tree:

![](http://lunarrabbit.com/img/posts/TerrainGenerationPost2/TreeExample.png)

For each of such sprite assets, we define
{% highlight c# %}
* Which layers this asset should be generated in
* How often the object generates
* How tall, relative to the height of the screen, the object should be on the layer closest to the foreground
* How much smaller it gets with each successive farther layer
{% endhighlight %}

All this information is used by a `BackgroundManager`, which handles generating the background environment assets. Here, we can set the colors of each layer as well as how densely packed the objects in each layer are. Typically, the further into the distance, the more tightly packed the environment assets should be. Environment asset density is determined by the minimum distance between each asset on a layer. The larger the minimum distance must be, the more loosely the environment assets are packed.

Each grouping of a single terrain piece and all the environment assets on that piece are called a `cluster`. Each layer consists of a series of `clusters`.

At the beginning of the game, for each cluster in each layer, the `BackgroundManager` generates a terrain piece. Then, it randomly selects an environment asset to generate based on what assets are available on the given layer and how rare that asset is. The size that this asset should be at the given layer is calculated and saved. This object is then placed onto the terrain such that its edges are within the the bounds of the terrain piece. The next object is placed at `previousObject.extents + Random(minDistance, minDistance + randomizer) + currentObject.extents`. This continues, from left to right, until no object can be placed without being out of bounds of the terrain piece.

![](http://lunarrabbit.com/img/posts/TerrainGenerationPost2/ProceduralPlacementExample.jpg)

To prevent creating and generating objects at run time, when the game begins, a pool of all generatable objects is created. Environment assets are pulled as needed from the object pool as they are needed. The environment asset that should be generated at a given location is saved, and once the terrain piece that asset belongs to is about to come into view, the environment asset is pulled from the pool, scaled and colored, and put in place.