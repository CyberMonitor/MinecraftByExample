# MBE21_TILEENTITYRENDERER

This example shows how to use a `TileEntityRenderer`. It uses a simple block and a `TileEntity` to store the animation parameters.

It will show you:

1. how to create a TER and bind it to a `TileEntity`
1. how to correctly translate so that your `TileEntity` renders in the correct place
1. how to save and restore the rendering settings so that subsequent renders by vanilla aren't affected
1. how to set the typical settings for rendering

There are four different methods of rendering demonstrated in this example:
1. Drawing lines
1. Manually drawing quads
1. Rendering a vanilla-style Entity Model
1. Rendering a wavefront object (generated using Blender or equivalent)

Usage: Place the mbe21 block and then right click on it to cycle through the different types of rendering. 

The pieces you need to understand are located in:

* `StartupClientOnly` and `StartupClientOnly` classes
* `TileEntityMBE21`
* `TileEntityRendererMBE21`
* `RenderLines` to demonstrate rendering using lines
* `RenderQuads` to demonstrate rendering using manually-added quads
* `resources\assets\minecraftbyexample\textures\entity\mbe21_ter_cube.png` - texture for the quads rendering
* `ModelHourglass` and `RenderModelHourglass` to demonstrate rendering using Entity Models
* `resources\assets\minecraftbyexample\textures\model\mbe21_hourglass_model.png` -- texture for the hourglass

There are a number of supporting files for the example which are explained in earlier mbe examples.
* `resources\assets\minecraftbyexample\lang\en_US.lang` -- for the displayed name of the block
* `resources\assets\minecraftbyexample\blockstates\mbe21_tesr_block.json` -- for the blockstate definition (of the base)
* `resources\assets\minecraftbyexample\models\item\mbe21_tesr_block.json` -- the model for rendering the item
* `resources\assets\minecraftbyexample\textures\items\mbe21_tesr_item_icon.png` -- item icon

The block will appear in the Blocks tab in the creative inventory.

For background information on:

* OpenGL rendering--see [http://www.glprogramming.com/red/](http://www.glprogramming.com/red/)
* Lighting: see [Lighting](http://greyminecraftcoder.blogspot.com/2014/12/lighting-18.html)
* Entity Models: [Model Basics](https://greyminecraftcoder.blogspot.com/2020/03/minecraft-model-1144.html)

Useful vanilla classes to look at:
* RenderType and DefaultVertexFormats to see what information is needed for each type of rendering
* BeaconTileEntityRenderer
* CampfireTileEntityRenderer
* ItemRenderer has useful examples of item rendering

Examples of different custom RenderTypes:
[Botania Mod](https://github.com/Vazkii/Botania/blob/1.15/src/main/java/vazkii/botania/client/core/helper/RenderHelper.java#L102)

Useful tool to create Entity Models:
[Blockbench](https://blockbench.net/blog/)

## Common errors

###"Missing Model", "Missing texture" errors, Model is a pink-and-black cube, model is the right shape but is pink and black, etc:
These are caused when you have specified a filename or path which is not correct, typically:

1. you've misspelled it
1. the upper/lower case doesn't match
1. you've forgotten the resource domain, eg `blockmodel` instead of `minecraftbyexample:blockmodel`
1. the folder structure of your assets folders is incorrect
1. you've added a folder in a filename which forge automatically adds (eg "models/"), or haven't included one
   where you should have.  
1. you've included the extension where you shouldn't have, or didn't include it when you should have  (for example 
   .png for texture files loaded automatically vs loaded manually)
1. If you're using Wavefront Obj file, you need to edit the mtl file to contain the correct path to the texture
Sometimes the console will give you an error message with an appropriate clue (eg "can't find file xxx:yyy"), other 
times it fails silently or gives a message which is misleading.   Check all spellings of your json files and the
parameters in the files, letter by letter.  Check against an example mod what paths+filenames they have used.



### Rendering doesn't look right:
This is a relatively complex subject and can be difficult to troubleshoot. Generally:

The object is in the wrong spot, or doesn't rotate how you expect:

1. your translation is wrong
1. your order of transformations (rotations, translations, scaling etc) are wrong. See Chapter 3 OpenGL red book: [http://www.glprogramming.com/red/chapter03.html](http://www.glprogramming.com/red/chapter03.html)

###If the object is too light or too dark:
1. one or more of the openGL settings is incorrect (especially `GL_LIGHTING`)
1. the multitexturing "brightness" (blocklight/skylight) is not right (see `OpenGlHelper.setLightmapTextureCoords`)
1. your `GlStateManager.color(red, green, blue)` is wrong

###You can't see parts of your object from one side:
1. If you want your faces to be visible on both sides, you need to disable `GL_CULL_FACE`.
1. If your face should be one-sided, but is facing the wrong way, you need to reverse the order of the points for that face. For example, you have specified points in the order A, B, C, D --> change the order to D, C, B, A

###Console error message: "Not filled all elements of the vertex":
Triggered at endVertex(), eg
    vertexBuilder
            .pos(matrixPos, x, y, z) 
            .color(red, green, blue, alpha)      
 here-->    .endVertex();    

Your vertex building code doesn't exactly match the VertexFormat of the IVertexBuilder.  Either
1. One of the builder methods is missing, eg
    vertexBuilder
            .pos(matrixPos, x, y, z) 
            .endVertex();
or
1. The builder methods are in the wrong order relative to the VertexFormat definition, eg
    vertexBuilder
            .color(red, green, blue, alpha)        
            .pos(matrixPos, x, y, z) // position coordinate
            .endVertex();

