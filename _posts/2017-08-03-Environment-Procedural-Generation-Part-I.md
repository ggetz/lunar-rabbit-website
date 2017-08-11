---
layout: post
title: "Heartline Environment Procedural Generation Part I: Terrain Meshes"
date: 2017-08-03 10:30:12
categories: development
author: Laura Mo
---

*How we created the procedurally generated terrain meshes for Heartline.*

To create the continuous scrolling style that Heartline uses, we procedurally created every terrain mesh so that each piece of terrain would flow perfectly into the next. Each generated piece of terrain looks something like this:

![](http://lunarrabbit.com/img/posts/TerrainGenerationPost/TerrainExample.png)

The `height maps` for the meshes were generated using a simple midpoint formula method. Each terrain mesh must connect to the terrain mesh pieces behind and in front of it. Thus, the starting height for the terrain piece being generated is taken from the ending height of the terrain mesh that precedes it. The ending height is randomized as the terrain piece after the current one has not yet been created.

An array is initialized at x size, with each slot representing a point in the heightmap. The starting height is placed into the first slot, and the end height in the last slot.

The midpoint—the slot whose index is halfway between the beginning and the end of the array—receives a height that is 

{% highlight c# %}
(startHeight – endHeight)/2 + Random.Range (-roughness, roughness) 
{% endhighlight %}

"`(startHeight – endHeight)/2`" gives the height that the midpoint should be at on a line from the start position to the end position. 

"`Random.Range (-roughness, roughness)`" represents the random displacement that is applied to the midpoint’s height. The greater the value of roughness, the more varied the terrain will look.

![](http://lunarrabbit.com/img/posts/TerrainGenerationPost/Roughness.gif)

There are now two segments in the heightmap: from the start location to the first midpoint, and from the first midpoint to the end location. The midpoints for both segments will have their heights generated in the same way. 

Then in the four resulting segments, the process is repeated again. Over and over, until the `height map` array is completely filled out.

![](http://lunarrabbit.com/img/posts/TerrainGenerationPost/MidpointDisplacement.gif)

In Unity, a mesh requires an array of vertices, uv points, and triangles.

The vertices represent the locations of each vertex in the mesh. These will be used to generate the triangles, so to simplify that process, the order in which the vertices are added will alternate between a value from the ```height map```—used to generate the top of the mesh—and a height value of 0—to generate the flat bottom of the mesh. The terrain mesh is 2D, so the vertices are an array of `Vector2` objects.

To create the array of vertices, simply loop through the `height map`, and for each value:

{% highlight c# %}
Vertices.Add(new Vector2(i/resolution, heightmap[i]));
Vertices.Add(new Vector2(i/resolution, 0));
{% endhighlight %}

The `resolution` indicates how many points of the `height map` will be used to generate a single Unity unit length bit of the mesh. The higher the `resolution`, the smoother the transitions between the heights of the terrain will look.

The uv array dictates how textures will be applied to the mesh. It is generated the same way as the vertices, except the x values of the Vector2 should be:

{% highlight c# %}
 i * (1/heightmap.Length)
{% endhighlight %}

Finally, build the mesh by creating triangles using the vertices defined in the vertices array. Each number on the vertices in the figure below indicate the index in the vertic array. The triangle array is a list of ints containing the indexes of the vertice array. Each 3 ints will be considered a triangle in the mesh. 

Because the vertices in the array are in top bottom top bottom alternating order, we can create the triangles very easily, as shown in the figure below.

![](http://lunarrabbit.com/img/posts/TerrainGenerationPost/Triangulation.jpg)

Once all these arrays have been created, create a new Mesh and assign to it the vertices, uv, and triangles array. Call RecalulateNormals and RecalculateBounds to finish setting up the mesh, and the Mesh is complete. 

Using this method, we can generate meshes that flow seamlessly from one to another.