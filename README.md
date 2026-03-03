# CombineLayers

A [Glyphs 3](https://glyphsapp.com) filter plugin that combines paths from different masters and layers using boolean operations. Useful for creating cutout effects, stencil layers, inline/outline combos, and other multi-layer type designs at export time.

## How It Works

CombineLayers operates as an export filter. It takes the current master's outlines (A) and combines them with outlines from another master or layer (B) using a boolean operation. The result is applied non-destructively during font export — your source drawings are never modified.

## Boolean Operations

| Operation | Result | Preview |
|---|---|---|
| **Add** | Union of A and B (merge shapes together) | ![Add](images/Add%20(Union_%20A%20∪%20B).png) |
| **Exclusion** | A minus B (cut away where B overlaps A) | ![Exclusion](images/Exclusion%20(A%20minus%20B).png) |
| **Intersection** | A intersect B (keep only where A and B overlap) | ![Intersection](images/Intersection%20(A%20∩%20B).png) |

## Path Direction Options

Each boolean operation can be paired with a path direction option that controls how B's paths are interpreted before the operation:

| Option | Behavior |
|---|---|
| **Current** | Use B's paths as-is |
| **Revert** | Reverse all of B's path directions |
| **Positive** | Resolve B to its filled area (removeOverlap + correctPathDirection) |
| **Negative** | Resolve B, then reverse all directions |

![Current vs Revert](images/Current%20vs%20Revert.png)

## Installation

1. Download `CombineLayers.glyphsFilter.zip`
2. Unzip and double-click `CombineLayers.glyphsFilter`
3. Restart Glyphs

The plugin will appear under *Filter > Combine Layers*.

![Opening the Plugin](images/Opening%20the%20Plugin.png)

## Usage

This is the primary workflow for using CombineLayers in production.

### Step 1: Set Up Your Layers

Create extra layers in your glyphs that contain the shapes you want to combine. For example, add a layer called "Serifs" under your Regular master with serif shapes positioned on the glyph.

![Step 1: Set up your layers](images/Step%201_%20Set%20up%20your%20layers.png)

### Step 2: Open the Filter Dialog

Go to **Filter → Combine Layers**.

![Opening the Plugin](images/Opening%20the%20Plugin.png)

### Step 3: Check the Layers You Want to Combine

When you open the plugin, it scans your font and lists all available layers — both master layers and any extra (non-master) layers. The current master is always checked and grayed out as the base layer (A). Check each additional layer and choose the boolean + path operation for each one.

![The Plugin Dialog](images/The%20Plugin%20Dialog.png)

![Step 3: Check the layers you want to combine](images/Step%203_%20Check%20the%20layers%20you%20want%20to%20combine.png)

### Step 4: Click "Create Export Instance"

The plugin will:
1. Create a new export instance named `CombinedLayers1` (incrementing if one already exists)
2. Add a `Filter` custom parameter for each checked layer
3. Open **Font Info → Exports** and select the new instance

![Step 4: Click Create Export Instance](images/Step%204_%20Click%20_Create%20Export%20Instance_.png)

> [!IMPORTANT]
> **Match the instance axis values to the source master.**
> Every CombineLayers export instance must have axis values identical to the master it is based on. For example, if your Regular master sits at **Weight 400**, the generated CombineLayers instance must also be set to **Weight 400**. Mismatched values will cause the instance to interpolate from the wrong position, producing incorrect or distorted outlines at export time.

### Step 5: Export

When you export the font, Glyphs will apply the filters in order for each glyph. Each operation uses the result of the previous one as its new base layer (A).

### Understanding the Two Dropdowns

![Understanding the Two Dropdowns](images/Understanding%20the%20Two%20Dropdowns.png)

### Via Custom Parameters

You can also add the filter manually as a custom parameter on any instance:

**Format:**
```
Filter = CombineLayers;layerName;booleanOp;pathOp
Filter = CombineLayers;layerName;booleanOp;pathOp;parentMasterName
```

**Examples:**
```
Filter = CombineLayers;Effect;add;current
Filter = CombineLayers;Cutout;exclusion;positive
Filter = CombineLayers;Shadow;intersection;current;Regular
```

The `parentMasterName` parameter is only needed when referencing a non-master layer, to specify which master it belongs to.

### Stacking Filters

Multiple CombineLayers filters can be stacked on a single instance. They are applied in order, so each subsequent operation works on the result of the previous one.

```
Filter = CombineLayers;Shadow;add;current
Filter = CombineLayers;Cutout;exclusion;positive
```

## Use Case Examples

### Stencil Cut

![Stencil cut](images/Stencil%20cut.png)

### Masking with Intersection

![Masking with intersection](images/Masking%20with%20intersection.png)

### Adding Serifs to a Sans-Serif Base

![Adding serifs to a sans-serif base](images/Adding%20serifs%20to%20a%20sans-serif%20base.png)

## Compatibility

- Requires **Glyphs 3**
- Works with masters and non-master (bracket, brace, or custom) layers

## Credits

Based on [MergeLayer](https://github.com/schriftgestalt/MergeLayer) by Georg Seifert.

## License

Copyright 2026 Mans Greback.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

See the [LICENSE](LICENSE) file for details.
