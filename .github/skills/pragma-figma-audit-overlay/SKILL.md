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

### Execution efficiency

- Reuse successful preflight reads as audit evidence. Keep a run-local ledger
   of source IDs, page bounds, component mappings, variants, tokens, created IDs,
   and completed checks. Invalidate affected entries after edits, library/data
   changes, or a resumed session; never reuse stale geometry for placement.
- Parallelize independent read-only work (source screenshots/metadata and
   Pragma reads). Serialize canvas writes per file. Resolve shared component
   families once across multiple frames, but audit each frame's states and
   occlusion separately. Deduplication must not reduce inventory coverage.
- Batch `block_lookup` names for the relevant tier; use `tier: all` only for
   unresolved cross-tier discovery. Cache by block identity and tier, not just
   display name. Read detailed guidance where needed before choosing a mapping;
   do not treat a short or empty summary as evidence of a library gap.
- Discover tools once per session. Reuse already loaded tool definitions, but
   still load the required Figma guidance before every `use_figma` call.
   Respect advertised batch limits; when a response is clamped, request only
   omitted items instead of repeating the completed searches.
- Prefer bounded, source-scoped reads returning unique component keys, relevant
   properties, typography and bounds. Avoid whole-document dumps and repeated
   library-owned descendants; request extra detail only for unresolved mappings.
- Keep writes incremental by section. Where supported, return created IDs,
   measured bounds and a section screenshot from the same build call. Inspect
   that screenshot instead of fetching it again; combine related corrections
   only after the current section passes its focused check.
- On an unknown write outcome, inspect recorded IDs or a narrowly scoped name
   once before retrying. Reuse anything that materialized; do not duplicate it.
   If the clone path is confirmed broken, use the permitted fallback rather than
   repeatedly retrying the same clone. Never bypass a server restriction.
- Final checks remain mandatory: compare the source with its baseline; inspect
   the completed inventory and overlay at readable resolution; check wrapping,
   specimen overflow, front-layer coverage, overlay position and final canvas
   bounds after all content grows. Move only new artifacts if they collide.
   A screenshot URL or matching export dimensions alone is not visual validation.

### Preflight

Before writing, verify all of the following:

1. Pragma responds to a needed block/token read; reuse it in the mapping.
   Use `pragma info` only for troubleshooting, not as proof of graph access.
2. Figma MCP can read the supplied frame and exposes `figma-use` and
   `figma-generate-design` for the write workflow.
3. The Figma frame resolves to a readable frame with a recorded name and size.
4. Pragma Core/Apps and required application libraries are reachable through
   linked libraries or a targeted design-system search.
5. Inspect top-level canvas bounds and reserve non-overlapping positions for the
   inventory and annotated copy. Use a 160px gutter after the rightmost or
   lowest occupied bounds; do not place a new top-level frame over an existing
   frame, section, or group.

If any check fails, stop before canvas mutation and direct the user to the
repository setup guidance.

### Audit and overlay

1. Reuse preflight metadata and screenshots unless the source changed. Fetch
   only missing evidence. Inventory visible regions,
   typography, geometry, components, assets, and overlapping layers.
2. Resolve each component through `block_lookup` first. Record its canonical
   block name, usage guidance, and relevant anatomy/modifiers. Use this to make
   the mapping decision, but do not expose or invent component documentation
   URLs. Then use implementation lookups, Code Connect, existing linked screens,
   and design-system search. Record true gaps as foundation-based compositions
   rather than importing Vanilla.
3. Build `Pragma migration inventory - <screen>` at its reserved clear canvas
   position, then verify its bounds do not intersect an unrelated top-level
   artifact. Add live linked specimens,
   variant guidance, a 14px (`dimension.size.fontSize.300`) body scale,
   wrap-safe specification copy, and a compact `Pragma guidance` section with
   each mapped block's short usage note.
4. Duplicate the source and name it `<screen> - Migration map (annotated
   reference)` at its reserved clear canvas position. Verify its bounds do not
   intersect an unrelated top-level artifact. If `$fig.clone()` does not
   materialize, use one narrow fallback that clones only the source node. Never
   modify the original.
5. Add one absolute layer named `Annotations - Toggle visibility`. Put every
   label inside it, set `clipsContent: false`, and use fixed/fill label widths
   with `textAutoResize: 'HEIGHT'` so labels wrap rather than crop.
6. Perform an occlusion pass. Keep all source components in the inventory, but
   tag only visible front-layer components in the overlay. For partly covered
   components, label only the exposed portion.
7. Validate source immutability, duplicate dimensions, linked specimens,
   wrapped labels, collision-free top-level placement, and no Vanilla
   substitution.

## Report

Return the original, inventory, and annotated-copy IDs; mapped components with
their usage notes; known gaps; and the validation result.