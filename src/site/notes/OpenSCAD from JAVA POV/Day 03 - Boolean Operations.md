---
{"dg-metatags":{"description":"Learn how to combine and carve 3D shapes in OpenSCAD using union, difference, and intersection — the boolean operations that make real models possible.","title":"OpenSCAD from a Java POV — Day 3: Boolean Operations","og:title":"OpenSCAD from a Java POV — Day 3: Boolean Operations","og:type":"tutorial","og:article:author":"Hemant Bothra","og:article:tag":"openscad, java, 3d-printing, cad, tutorial","og:article:section":"Technology"},"dg-publish":true,"comments":true,"tags":["openscad","java","3d-printing","tutorial"],"source":null,"series":"OpenSCAD from a Java POV","permalink":"/open-scad-from-java-pov/day-03-boolean-operations/","metatags":{"description":"Learn how to combine and carve 3D shapes in OpenSCAD using union, difference, and intersection — the boolean operations that make real models possible.","title":"OpenSCAD from a Java POV — Day 3: Boolean Operations","og:title":"OpenSCAD from a Java POV — Day 3: Boolean Operations","og:type":"tutorial","og:article:author":"Hemant Bothra","og:article:tag":"openscad, java, 3d-printing, cad, tutorial","og:article:section":"Technology"},"dgPassFrontmatter":true}
---


# Day 3 — Boolean Operations: Carving Shapes Like a Programmer

**⏱ 15 min read + hands-on**

---

## Quick Recap

You can place primitives and move them with `translate`, `rotate`, and `scale`. But real-world objects aren't just floating spheres and boxes — they have holes, merged bodies, and intersecting geometry. That's what today covers.

---

## The Big Three: `union`, `difference`, `intersection`

OpenSCAD gives you three Boolean set operations on geometry. If you've ever worked with sets in Java (`Set.addAll`, `Set.retainAll`, `Set.removeAll`), the mental model transfers directly.

| OpenSCAD | Set equivalent | What it does |
|---|---|---|
| `union()` | A ∪ B | Merge shapes into one solid |
| `difference()` | A − B | Subtract one shape from another |
| `intersection()` | A ∩ B | Keep only the overlapping region |

All three use the same **child scope** syntax you learned with transforms — the operation wraps the shapes it applies to:

```scad
operation() {
    shape_A();
    shape_B();
}
```

Let's look at each one on its own first.

---

## `union()` — Merging Shapes

`union()` joins all children into one continuous solid.

```scad
union() {
    cube([20, 10, 5]);
    translate([10, 5, 5])
        sphere(r = 6, $fn = 32);
}
```

A box with a sphere fused on top. They become one solid — no seam, no internal boundary. Run it and rotate the view to confirm they're merged, not just touching.

> **Worth knowing:** Multiple shapes at the top level of a file are *implicitly* unioned. Writing `union()` explicitly does the same thing — you'll need the explicit form once we combine operations later.

---

## `difference()` — Cutting Holes

`difference()` takes the **first child** as the base solid and subtracts every child after it.

```scad
difference() {
    cube([30, 30, 10]);
    translate([15, 15, -1])
        cylinder(h = 12, r = 5, $fn = 32);
}
```

A box with a circular hole through it. The cylinder is the drill bit — it never appears in the result.

**Critical rule: order matters.** First child = base. Everything else = cutter. Swap them and you get the inverse — a cylinder with a box-shaped void.

> **Java analogy:** Think of the first child as `this` — the object being modified. The rest are arguments. `difference(A, B, C)` = `A - B - C`.

### The Z-fighting trick

Make your cutter slightly **taller** than the base and start it 1 mm below (`z = -1`). This prevents z-fighting — a rendering flicker that happens when two faces sit exactly on the same plane.

```scad
difference() {
    cube([30, 30, 10]);             // base is 10mm tall
    translate([15, 15, -1])
        cylinder(h = 12, r = 5, $fn = 32);  // cutter: 12mm, starts at -1
}
```

You'll use this pattern every single time you drill a hole. File it away now.

---

## `intersection()` — Keeping Only the Overlap

`intersection()` keeps only the volume that exists inside **all** children at the same time.

```scad
intersection() {
    sphere(r = 15, $fn = 32);
    cube([22, 22, 22], center = true);
}
```

Result: a sphere with its corners shaved flat — a rounded cube. The cube acts as a boundary; anything outside it is discarded. This is a clean trick for rounded shapes without complex maths.

---

## Combining Them — A Real Example

Now that each operation makes sense on its own, here's where they work together.

Say you want a **base plate with a cylindrical boss on it, and a bolt hole through the boss**. That's three ideas: merge plate + boss, then drill a hole through the combined body.

```scad
difference() {
    // Base: plate and boss merged into one solid first
    union() {
        cube([40, 30, 6]);                              // flat plate
        translate([20, 15, 6])
            cylinder(h = 10, r = 6, $fn = 32);         // boss on top
    }
    // Drill a hole through the boss (and into the plate)
    translate([20, 15, -1])
        cylinder(h = 18, r = 3, $fn = 32);
}
```

Why the `union()` here? Without it, `difference()` would treat the boss cylinder as a cutter and subtract it from the plate — the opposite of what you want. The `union()` tells OpenSCAD: *these two shapes together are the base*, before anything gets subtracted.

This `difference( union(...), cutter )` pattern comes up constantly in real models.

---

## Today's Exercise

Build a **hollow box** — open at the top, with walls and a floor:

- Outer dimensions: 30 × 30 × 20
- Wall and floor thickness: 3mm

Use `difference()` with a smaller cube as the cutter. Think carefully: the cutter needs to leave a 3mm floor, so it shouldn't start at `z = 0`.

Expected result: rotate the view and look down — you should see a hollow interior with walls and a solid base.

---

## Key Takeaway

Learn each Boolean cleanly before combining them. `difference()` is what you'll reach for most — base first, cutters after, always extend ±1mm to avoid z-fighting. `union()` merges solids, `intersection()` keeps overlaps. When you need to subtract from a composite body, wrap the base in `union()` first — that's the one case where they combine.

Tomorrow: **variables and parameters** — making your models reusable and dimension-driven, which is where OpenSCAD starts to feel genuinely like programming.

---

