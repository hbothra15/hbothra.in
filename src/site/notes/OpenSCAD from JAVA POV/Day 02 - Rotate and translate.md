---
{"dg-metatags":{"description":"Learn how to move, rotate, and scale shapes in OpenSCAD — the spatial transforms every Java developer needs to place geometry precisely.","title":"OpenSCAD from a Java POV — Day 2: Moving Things Around","og:title":"OpenSCAD from a Java POV — Day 2: Moving Things Around","og:type":"tutorial","og:article:author":"Hemant Bothra","og:article:tag":"openscad, java, 3d-printing, cad, tutorial","og:article:section":"Technology"},"dg-publish":true,"comments":true,"tags":["openscad","java","3d-printing","tutorial"],"source":null,"series":"OpenSCAD from a Java POV","permalink":"/open-scad-from-java-pov/day-02-rotate-and-translate/","metatags":{"description":"Learn how to move, rotate, and scale shapes in OpenSCAD — the spatial transforms every Java developer needs to place geometry precisely.","title":"OpenSCAD from a Java POV — Day 2: Moving Things Around","og:title":"OpenSCAD from a Java POV — Day 2: Moving Things Around","og:type":"tutorial","og:article:author":"Hemant Bothra","og:article:tag":"openscad, java, 3d-printing, cad, tutorial","og:article:section":"Technology"},"dgPassFrontmatter":true}
---


# Day 2 — Moving Things Around: `translate`, `rotate`, `scale`

**⏱ 15 min read + hands-on**

---

## Quick Recap

Yesterday you learned OpenSCAD is declarative — you describe shapes, not procedures. You placed `cube`, `sphere`, and `cylinder` at the origin. Today's problem: the origin gets crowded fast. You need to move things.

---

## The Transform Syntax — Read This First

OpenSCAD transforms work differently from what you'd expect in Java. They use **child scope syntax** — the transform *wraps* the shape it applies to:

```scad
translate([10, 0, 0])
    cube([5, 5, 5]);
```

> **Java analogy:** Think of it like a decorator / wrapper — the transform is applied to whatever is inside its scope. There's no `shape.translate(...)` method chain. The transform *is* the outer context.

This trips up Java devs. There is no object to call methods on. The shape is a child of the transform.

---

## `translate` — Move a Shape

`translate([x, y, z])` shifts the child shape by the given vector.

```scad
// A cube at the origin
cube([10, 10, 10], center = true);

// The same cube moved 20mm to the right along X
translate([20, 0, 0])
    cube([10, 10, 10], center = true);
```

OpenSCAD axes:
- **X** → left/right
- **Y** → forward/back
- **Z** → up/down (this is your height axis for 3D printing)

---

## `rotate` — Spin a Shape

`rotate([x_deg, y_deg, z_deg])` rotates around each axis by the given degrees.

```scad
// Cylinder standing upright (default — along Z axis)
cylinder(h = 20, r = 5);

// Cylinder lying on its side (rotated 90° around Y)
rotate([0, 90, 0])
    cylinder(h = 20, r = 5);
```

Rotation follows the **right-hand rule** — curl the fingers of your right hand in the direction of rotation, thumb points along the positive axis.

You can also rotate around a single axis with a scalar + axis form:

```scad
rotate(a = 45, v = [0, 0, 1])  // 45° around Z axis
    cube([10, 5, 5]);
```

---

## `scale` — Resize a Shape

`scale([x, y, z])` stretches or shrinks along each axis. Values are **multipliers**, not absolute sizes.

```scad
// A sphere squashed to half height
scale([1, 1, 0.5])
    sphere(r = 10);

// Uniform scale — make it twice as big
scale([2, 2, 2])
    sphere(r = 10);
```

> **Prefer `resize` over `scale` when you want absolute dimensions** — `resize([20, 20, 10])` sets the bounding box directly, no mental arithmetic needed.

---

## Chaining Transforms

You can stack transforms — they apply **inside-out**, closest to the shape first, then outward:

```scad
translate([30, 0, 0])
    rotate([0, 0, 45])
        cube([10, 10, 10], center = true);
```

Read this as: take the cube → rotate it 45° around Z → then move it 30mm along X.

> **Java analogy:** Like nested decorators or middleware — the innermost one wraps the object first, then each outer one wraps the result.

Order matters. Translate-then-rotate gives a different result from rotate-then-translate — exactly like matrix multiplication, which is what OpenSCAD is doing under the hood.

---

## Applying a Transform to Multiple Shapes

Wrap multiple children in a block `{}`:

```scad
translate([20, 0, 0]) {
    cube([10, 10, 10], center = true);
    sphere(r = 7);
}
```

Both shapes get translated together. This is your grouping mechanism — no class, no list, just a scope block.

---

## Today's 15-Minute Exercise

Build a simple dumbbell shape:

1. A `cylinder` of height 30mm, radius 4mm — the bar — centred at the origin, standing upright.
2. A `sphere` of radius 10mm at the **top** of the bar.
3. A `sphere` of radius 10mm at the **bottom** of the bar.

No overlap, no gap between the spheres and bar ends. Work out the Z positions yourself — this is the core spatial reasoning skill you'll use constantly.

Expected result: a symmetric dumbbell standing on the Z axis.

---

## Key Takeaway

Transforms in OpenSCAD are **wrappers, not method calls**. They apply to their child scope. Stack them inside-out, and group with `{}` when you need to move multiple shapes together. Getting comfortable with `translate` + `rotate` unlocks 80% of practical 3D modelling.

Tomorrow: combining shapes with `union`, `difference`, and `intersection` — the boolean operations that let you cut holes and merge solids.

---
