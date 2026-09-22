---
name: pragma-figma-audit-overlay
description: "Use when auditing a legacy Figma screen for a Pragma migration, creating a linked Pragma component and typography inventory with specimens, or duplicating and annotating a source frame with an overlay migration map. Preserves the source frame and never substitutes Vanilla components."
argument-hint: "Figma file key, original frame node ID, and optional reconstructed frame node ID"
---

# Pragma Figma Audit and Overlay

Create a migration-ready Figma artifact: a preserved annotated source copy plus
a linked Pragma component, typography, and implementation inventory.

## Preconditions

- The user provides a Figma `fileKey` and original frame `nodeId`.
- Treat the original as immutable.
- Use the official Figma MCP; load `figma-use` before every `use_figma` call.
- Use the Pragma MCP or CLI tools to resolve blocks, tokens, implementations,
   and application-level patterns before writing to Figma.
- Use actual linked Pragma, Pragma Apps, or Launchpad Pragma instances. Never
  substitute Vanilla for a missing Pragma component.

## Workflow

### Preflight

Before writing, verify all of the following:

1. Pragma responds to `pragma info` or a small Pragma MCP block/token read.
2. Figma MCP can read the supplied frame and exposes `figma-use` and
   `figma-generate-design` for the write workflow.
3. The Figma frame resolves to a readable frame with a recorded name and size.
4. Pragma Core/Apps and required application libraries are reachable through
   linked libraries or a targeted design-system search.

If any check fails, stop before canvas mutation and direct the user to the
repository setup guidance.

### Audit and overlay

1. Capture source metadata and a screenshot. Inventory visible regions,
   typography, geometry, components, assets, and overlapping layers.
2. Resolve each component through `block_lookup` first. Record its canonical
   block URI, usage guidance, and relevant anatomy/modifiers; this URI is the
   official Pragma documentation reference. Then use implementation lookups,
   Code Connect, existing linked screens, and design-system search. Record true
   gaps as foundation-based compositions rather than importing Vanilla.
3. Build `Pragma migration inventory - <screen>` with live linked specimens,
   variant guidance, a 14px (`dimension.size.fontSize.300`) body scale,
   wrap-safe specification copy, and a compact `Pragma docs` section listing
   each mapped block's canonical URI and short usage note.
4. Duplicate the source and name it `<screen> - Migration map (annotated
   reference)`. If `$fig.clone()` does not materialize, use one narrow fallback
   that clones only the source node. Never modify the original.
5. Add one absolute layer named `Annotations - Toggle visibility`. Put every
   label inside it, set `clipsContent: false`, and use fixed/fill label widths
   with `textAutoResize: 'HEIGHT'` so labels wrap rather than crop.
6. Perform an occlusion pass. Keep all source components in the inventory, but
   tag only visible front-layer components in the overlay. For partly covered
   components, label only the exposed portion.
7. Validate source immutability, duplicate dimensions, linked specimens,
   wrapped labels, and no Vanilla substitution.

## Report

Return the original, inventory, and annotated-copy IDs; mapped components with
canonical Pragma documentation URIs; known gaps; and the validation result.