---
{"dg-metatags":{"description":"Learn OpenSCAD from a Java programmer's perspective — Day 1 covers what OpenSCAD is, how to set it up, and your first 3D primitives in 15 minutes.","title":"OpenSCAD from a Java POV — Day 1: Hello, Cube","og:title":"OpenSCAD from a Java POV — Day 1: Hello, Cube","og:type":"tutorial","og:article:author":"Hemant Bothra","og:article:tag":"OpenSCAD, Java, 3D Printing, CAD, Tutorial","og:article:section":"Technology"},"dg-publish":true,"comments":true,"tags":["openscad","java","3d-printing","tutorial"],"source":null,"series":"OpenSCAD from Java POV","permalink":"/open-scad-from-java-pov/day-01-hello-cube/","metatags":{"description":"Learn OpenSCAD from a Java programmer's perspective — Day 1 covers what OpenSCAD is, how to set it up, and your first 3D primitives in 15 minutes.","title":"OpenSCAD from a Java POV — Day 1: Hello, Cube","og:title":"OpenSCAD from a Java POV — Day 1: Hello, Cube","og:type":"tutorial","og:article:author":"Hemant Bothra","og:article:tag":"OpenSCAD, Java, 3D Printing, CAD, Tutorial","og:article:section":"Technology"},"dgPassFrontmatter":true}
---


# Day 1 — Hello, Cube: What is OpenSCAD and Why It Clicks for Java Programmers

> **15-minute lesson** | Series: OpenSCAD from Java POV

---

## What is OpenSCAD?

OpenSCAD is a **code-first 3D CAD tool**. Unlike Blender or Fusion 360 — where you drag, sculpt, and click your model into shape — in OpenSCAD you *write a program* that describes geometry. The tool compiles it into a 3D model you can export and 3D print.

As a Java programmer, this is going to feel far more natural than you might expect. OpenSCAD is essentially a **declarative geometry DSL** — you describe *what* the shape is, not *how* to draw it. No mouse gymnastics, just code.

---

## Setup (5 min)

1. Download from [openscad.org](https://openscad.org/downloads.html) — free, cross-platform
2. Open the app. You'll see two panels:
   - **Left:** code editor
   - **Right:** 3D preview
3. Key shortcuts:
   - `F5` — fast preview render
   - `F6` — full render (slower, export-quality)
   - Mouse drag to rotate the 3D view

That's all setup you need for now.

---

## Your First Program (5 min)

Type this in the editor and hit `F5`:

```openscad
cube([20, 10, 5]);
```

You'll see a rectangular box — 20mm wide, 10mm deep, 5mm tall. Now try replacing it with:

```openscad
sphere(r = 15);
```

And then:

```openscad
cylinder(h = 30, r = 10);
```

These three — `cube()`, `sphere()`, `cylinder()` — are the fundamental building blocks. Every complex model is ultimately built by combining and carving these primitives.

---

## Why is it called `cube` when I'm making a rectangle?

Fair question — it's one of OpenSCAD's naming quirks. `cube()` actually creates a **rectangular box (cuboid)**. The name refers to the fact that it *can* produce a cube, not that it *always* does.

```openscad
cube(10);           // true cube — 10×10×10 (single number shorthand)
cube([20, 10, 5]);  // rectangular box — width × depth × height
```

Think of it like Java's `Arrays.asList()` — the name implies a specific thing, but it's really a general-purpose utility. You'll use the `[x, y, z]` form almost always.

---

## Understanding `translate` — Moving Things Around

By default, every primitive in OpenSCAD spawns at the **origin (0, 0, 0)**. If you place a cube and a sphere without moving either, they'll overlap at the centre.

`translate([x, y, z])` shifts whatever comes after it by those distances. Think of it as **setting a coordinate offset** before placing an object.

```openscad
// Without translate — sphere and cube overlap at origin
cube([20, 20, 5]);
sphere(r = 8);      // centre is at [0,0,0] — buried inside the cube

// With translate — sphere sits on top
cube([20, 20, 5]);
translate([10, 10, 13]) sphere(r = 8);  // moved to centre of cube top face
```

**Java analogy:** Imagine a `Graphics2D.drawRect()` call where you always specify absolute `x, y` coordinates. In OpenSCAD, instead of baking coordinates into each primitive, you *wrap* the primitive with a translate — similar to applying a transformation matrix before drawing.

```openscad
// OpenSCAD pattern
translate([x, y, z])
    someShape();

// Mentally reads like:
// "Move the origin to [x,y,z], then place someShape there"
```

Key thing to internalise: **translate doesn't move an existing object — it sets the coordinate context for what's drawn inside it.** The indentation convention makes this clear — anything indented under a translate is affected by it.

---

## Today's Exercise (5 min)

Write a program that builds a simple snowman — two shapes stacked:

- A `cube` base: 20×20×5 (a flat platform)
- A `sphere` sitting on top: radius 8, centred at `z = 5 + 8 = 13`

```openscad
// Platform — a 20×20×5 rectangular box
cube([20, 20, 5]);

// Sphere on top — centre it over the platform, above the top face
// Platform top face is at z=5. Sphere radius is 8, so centre at z=13.
// Platform centre in x,y is at [10, 10] (half of 20×20)
translate([10, 10, 13])
    sphere(r = 8);
```

Run it with `F5`. Rotate the view with your mouse. You should see a ball sitting on a flat platform.

---

## Key Takeaway

OpenSCAD is geometry as code. Your Java instincts — modular thinking, parameterisation, clean structure — are a direct advantage here. The syntax is lightweight; the hard part is shifting from imperative to declarative thinking. That shift starts today.

---

*Next up — Day 2: The three core primitives in depth. cube, sphere, and cylinder — every parameter, every edge case, and when to use which.*
