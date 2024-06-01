---
title: "Godot 4.1 is here, smoother, more reliable, and with plenty of new features"
excerpt: "Four months after the release of Godot 4.0, we are excited to bring you Godot 4.1 — an update with a focus on stability, performance, and polish."
categories: ["release"]
author: "Godot contributors"
image: /storage/blog/covers/godot-4-1-is-here.webp
date: 2023-07-06 12:00:00
---

After [four months of work](/article/godot-4-0-sets-sail), we are excited to bring you Godot 4.1! It's an update that follows our pledge to improve upon Godot 4.0 with frequent incremental releases — with a focus on stability, performance, and polish.

As always, a new release comes with a bunch of welcome new features, like the improved AI navigation avoidance and the ability to detach code editors and put them on other displays.

![The code editor detached from the main editor window](/storage/blog/godot-4-1-is-here/detached-code-editor.png)

Still, we took great care to prioritize the bugs you have encountered in 4.0. This update fixes over 900 issues that users have reported from using Godot 4 or while helping contributors test 4.1 with pre-release builds. The engine should feel more reliable overall. We will continue improving stability, performance, and workflows with every upcoming feature release of Godot 4.

For most games and apps made with 4.0 it should be relatively safe to migrate to 4.1. We are [preparing a migration guide](https://github.com/godotengine/godot-docs/pull/7611) that outlines everything you need to pay attention to when migrating your project. Some incompatibilities are expected for C# and GDExtension users specifically, however we are working on making sure to avoid that in future releases. Don't forget to always make backups when moving versions, even minor. Better yet, prefer using a version control system, such as Git, and commit a version of your project before the migration.

**If you wish to get straight into the action, you can [download Godot 4.1 now](https://godotengine.org/download/).** Or stick around and read more about the most notable changes of this release. You can also watch this great highlights video by [GDQuest](https://www.gdquest.com/):

<iframe width="560" height="315" src="https://www.youtube.com/embed/PAtG_fHhIx8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Giving back

As a community effort, Godot relies on individual contributors to improve. In recent years, [user and company donations](https://godotengine.org/donate/) allowed us to also hire a number of core contributors to work full-time on the engine and bring you these fast-paced releases.

Our monthly expenses remain higher than monthly donations, and we still depend on large one-time company donations to fund development. Currently, we need a lot more [monthly donations](https://godotengine.org/donate/) to keep up the pace with Godot 4 updates, not to mention the need to hire more maintainers to review every contribution.

Besides financial support, you can also give back by: writing detailed bug reports, contributing to the code base, writing documentation, writing guides (for the docs or on your own website), and supporting others on the various [community platforms](https://docs.godotengine.org/en/latest/community/channels.html) by answering questions and providing helpful tips.

Last but not least, making games with Godot and crediting the engine goes a long way to help make it more popular and convince more people to contribute and improve Godot for everyone. Remember, we are all in this together and Godot requires community support in every area to thrive.

## Highlights

This release was made possible thanks to submissions from over 300 contributors! We warmly thank you all for your work and dedication, including those who helped us test the engine before the release and submit bug reports.

For an exhaustive list of all the bug fixes and improvements, head on over to our [interactive changelog](https://godotengine.github.io/godot-interactive-changelog/#4.1).

Without further ado, here's a breakdown of the main changes and new features.

### Performance

Godot games are built as a tree of nodes, which are the engine's base building block for game entities. Adding and removing nodes are operations the engine needs to do extremely often, so they need to be as fast as possible.

In 4.1, we changed the algorithm to use a fast hashmap to make adding and removing child nodes several times faster. Uncommon node operations are slightly slower as a result, and the memory footprint of the base Node class is 10% higher, but this is a small and necessary trade-off for a big benefit to all Godot users, especially to more complex projects with a lot of node manipulation.

This version also introduces experimental multithreading for your scenes, a feature kickstarted by [Juan Linietsky](https://github.com/reduz). New node properties give you full control over how nodes get processed, sequentially or in parallel.

![Thread group configuration options for Node](/storage/blog/godot-4-1-is-here/threaded-node-properties-highlighted.png)

As mentioned, the feature is currently marked experimental as it needs extensive testing to find edge cases. We do not recommend using it in production yet. But it sets the foundation for making the most of modern hardware, with simple controls.

Also on the multithreading front, [Pedro J. Estébanez](https://github.com/RandomShaper) worked hard on fixing a large amount of multi-threaded loading issues and limitations.

On the rendering side, the Vulkan renderer got a pipeline cache. While Godot already cached shaders to reduce shader compilation stutter, compiling pipelines still lead to some stuttering and slower load times. While the pipeline compilation stuttering issue is far from solved, this represents a step in the right direction and should also lead to a slight decrease in load times when using one of the RenderingDevice-based rendering backends. This cache can be toggled off in the project settings and is on by default in Godot 4.1.

![Shader cache project settings](/storage/blog/godot-4-1-is-here/shader-cache-setting.png)

This improvement doesn't address all possible causes of stalls, but it's a first step in the right direction by [Alexander Streng](https://github.com/warriormaster12). We'll keep working on it in upcoming releases.

More work will follow on the performance front throughout the year.

### Core

When importing models into Godot, there was often the problem that they ended up facing backward.

![A model facing backwards (before the change)](/storage/blog/godot-4-1-is-here/model-facing-back.webp)

[Juan Linietsky](https://github.com/reduz), [Tokage](https://github.com/TokageItLab), and [Aaron Franke](https://github.com/aaronfranke) worked on a number of ways to address this issue. One of the implemented changes swaps the front and back camera directions in the editor. Also, the `look_at()` function now has an argument to use the model space as the reference for looking forward instead of the camera's minus Z axis. These changes also help fix a long-standing bug with path following.

![A model facing forward (after the change)](/storage/blog/godot-4-1-is-here/model-facing-front.webp)

This update also brings frame delta smoothing to Godot 4, by [lawnjelly](https://github.com/lawnjelly). This can significantly improve the fluidity of motion and give smoother gameplay. This option is enabled by default, though please note that it only works when VSync is also enabled.

### Scripting

#### GDScript

Until now, in GDScript, you needed to use a resource or an autoload to share data between multiple instances of the same script.

Thanks to [George Marques](https://github.com/vnen), you can now create and use static variables instead. Static variables store data on the class instead of each instance, so they're shared between every instance of the class.

To make a variable static, add the `static` keyword in front of a variable defined at the top of your script.

![A GDScript code sample showcasing the use of static variables](/storage/blog/godot-4-1-is-here/gdscript-static-variables.png)

A great feature of GDScript added in Godot 4 is the automatic generation of documentation pages for your named classes.

![An example of a generated documentation based on a custom GDScript class](/storage/blog/godot-4-1-is-here/gdscript-docs-generation.png)

This version includes a rework of the system by [ocean](https://github.com/anvilfolk) that now treats enumerations as types, making the generated documentation more precise. You can also now use inline docstrings instead of having to always place them above a variable or a function's definition.

```gdscript
var my_variable = 10 ## This is an inline docstring
```

#### C\#/.NET

The focus in this release was on bringing feature parity between C# and GDScript.

When using GDScript, you can define a new node type to use in the editor by adding a global class name to your script.

Starting from Godot 4.1, this is also possible in C# by adding the `[GlobalClass]` attribute to your file, thanks to [Raul Santos](https://github.com/raulsntos) and [Will Nations](https://github.com/willnationsdev). You can also use the `[Icon]` attribute to give your global class a unique icon.

Note that as of this release projects made with C# still cannot be exported to mobile and web platforms. We are working on providing the support as soon as possible, but the resolution of this limitation will likely depend on the release of .NET 8 at the end of 2023. This means that the work on enabling mobile and web platforms can only truly start later this year.

#### GDExtension

Godot comes with a unique technology to use low-level languages like C++ as a game scripting language, without having to recompile the engine.

While the technology is still in beta for this release, GDExtension is now even closer to GDScript and C# in terms of scripting capabilities. You can now implement new visual shader nodes and create editor plugins with GDExtension.

![An example of using a custom visual shader node from GDExtension](/storage/blog/godot-4-1-is-here/gdextension-visual-shader-node.png)

The team also implemented a backward compatibility system to help ensure that code written for Godot 4.1 keeps working even if the API changes in future releases. However, a significant compatibility breakage was necessary to do in 4.1 to fix critical issues in GDExtension, so existing Godot 4.0 extensions will need to be ported and recompiled for 4.1.

Finally, a lot of work was done on the architecture once again to make the GDExtension API extensible in the future.

All the above resulted from the teamwork of [David Snopek](https://github.com/dsnopek), [Juan Linietsky](https://github.com/reduz), [RedworkDE](https://github.com/RedworkDE), [Yuri Rubinsky](https://github.com/Chaosus), and [Yuri Sizov](https://github.com/YuriSizov).

### Editor

Godot 4 has support for multiple windows which we use for the project and editor settings and various pop-ups. Starting with this version, you can now also detach docs into floating windows and detach script editors, including the shader editor, and place them on a separate monitor.

![The code editor detached from the main editor window](/storage/blog/godot-4-1-is-here/detached-code-editor.png)

In addition to that, the editor will now keep track of your window layout so that when you close and reopen the editor, you should often find yourself exactly where you left off.

You can thank [trollodel](https://github.com/trollodel), [Hendrik Brucker](https://github.com/Geometror), and [Tomasz Chabora](https://github.com/KoBeWi) for this.

Godot 4.0 introduced the option to define and export typed arrays, and to export individual nodes to the inspector, but it was not possible to combine the two. From Godot 4.1 onwards, you can export arrays of nodes to the inspector, which is great to link game objects together.

![An array of nodes exported as a custom property](/storage/blog/godot-4-1-is-here/editor-exported-node-array.png)

This work was done by Tomasz and [Timothé Bonhoure](https://github.com/ajreckof)

The project manager now allows you to assign tags to individual projects and filter projects by tags. It makes it much easier to search through dozens, if not hundreds of Godot projects.

![A list of projects with custom tags assigned to them](/storage/blog/godot-4-1-is-here/editor-project-manager-tags.png)

### Rendering

Particle turbulence got reworked in this version based on artists' feedback to offer greater creative control. Turbulence is used extensively to create these rich sprawling effects seen in many modern games. Big thanks to [KdotJPG](https://github.com/KdotJPG) and [Raffaele Picca](https://github.com/RPicster) for this contribution.

![An effect driven by the updated particle turbulence system](/storage/blog/godot-4-1-is-here/render-particles-turbulence.png)

Using the new 3D noise textures you can control the density of volumetric fog easily, and make it thinner in certain areas. NoiseTexture3D can also be used to create particle attractor vector fields, which is useful to simulate wind that affects particles. [Lasuch](https://github.com/Lasuch69) and [Clay John](https://github.com/clayjohn) implemented this feature.

![A more complex fog volume using the new 3D noise](/storage/blog/godot-4-1-is-here/render-3d-noise.png)

While there are still many [planned improvements](https://godotengine.org/article/rendering-priorities-4-1/) to Godot's renderers, most will be present in future releases. The rendering team (and all rendering contributors) prioritized bug fixing and stability over new features. Notably, the 3D GLES3 renderer is not yet complete, but will see substantial work over the coming months.

### Navigation

Godot 4.0 introduced real-time avoidance for AI navigation, but it was limited to a single plane.

This release includes completely rewritten avoidance algorithms by [smix8](https://github.com/smix8) to give you much better behaviors and greater control. Avoidance can now happen in 2D or 3D, allowing flying agents to move over those walking on the ground.

![A demo of multiple navigation agents operating on several layers in 3D](/storage/blog/godot-4-1-is-here/navigation-3d-layers.png)

On top of that, you can now use layers to control which agents avoid which and assign priorities to have some agents push others away.

![A demo of multiple navigation agents with varied priorities](/storage/blog/godot-4-1-is-here/navigation-priority.png)

Check out the [updated documentation](https://docs.godotengine.org/en/stable/tutorials/navigation/index.html) to learn more about how the improved navigation works.

### Platform support

We are as committed as ever to have Godot games on all popular platforms, and 4.1 still offers the same capabilities as did Godot 4.0. You can export to all desktop platforms with both standard and .NET version of the engine, and you can export to mobile and web if you don't use C# in your projects.

As of 4.1 web exports still have some limitations due to poor vendor support of certain modern features. Browsers with bad WebGL 2 support, such as Chrome on macOS, unfortunately suffer from issues which we cannot address without a fix from Google or a significant amount of effort put into supporting a dedicated WebGL 1 / GLES2 renderer.

We are also aware of the complexity setting up web games on hosting platforms which don't let you set the required CORS headers for SharedArrayBuffer support. This mostly depends on Safari implementing the `coep:credentialless` header support, while Chromium-based browsers and Firefox already work fine (especially if you publish on itch.io). There is a [possible workaround](https://github.com/godotengine/godot-proposals/issues/6616) that we are investigating.

## Known issues

With every release we acknowledge that there are going to be problems which we couldn't resolve in time. Some of these problems have been identified and fixes are being worked on as we speak, while others remain unknown until someone runs into the issue. You can follow our [bug tracker](https://github.com/godotengine/godot/issues) to learn if the problem that you're experiencing has been reported. We appreciate new reports and confirmations of existing reports, as that helps us prioritize fixing specific issues.

Here are notable issues that have already been identified that you might experience in Godot 4.1:

- The newly added API for defining custom configuration properties for export presets does not persist user configurations. Configuration options get reset and lost on editor restarts. The fix is going to be available in 4.1.1 (see [GH-79025](https://github.com/godotengine/godot/pull/79025)).

## On to the next release!

We already started work on Godot 4.2, which will come out in four months. We are dedicated to keeping up the pace and sticking to this reliable release cycle, so you never have to wait long for the next batch of improvements and new features.

Until then, [enjoy Godot 4.1](https://godotengine.org/download/)!
