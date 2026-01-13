# MyCap Roadmap

Personal fork of [Cap](https://github.com/CapSoftware/Cap) with monetization removed and Screen Studio-inspired features planned.

## Current Custom Changes
- ✅ Removed recording time limits
- ✅ Stripped upgrade prompts from UI

---

## Architecture Overview

### Rendering Pipeline
```
Capture → Decoding → Layer Compositing → GPU Shaders → Export
```

### Key Directories
- `crates/rendering/src/` - Core rendering engine (Rust + WGPU)
- `crates/rendering/src/layers/` - Layer system (background, cursor, video)
- `crates/rendering/src/shaders/` - WGSL GPU shaders
- `apps/desktop/src/routes/editor/` - Editor UI (React/SolidJS)

### Critical Files Reference
| Feature | Implementation File |
|---------|---------------------|
| Background layer | `crates/rendering/src/layers/background.rs` |
| Cursor layer | `crates/rendering/src/layers/cursor.rs` |
| Cursor smoothing | `crates/rendering/src/spring_mass_damper.rs` |
| Cursor interpolation | `crates/rendering/src/cursor_interpolation.rs` |
| Drop shadows | `crates/rendering/src/shaders/composite-video-frame.wgsl` |
| Shadow UI controls | `apps/desktop/src/routes/editor/ShadowSettings.tsx` |
| Zoom system | `crates/rendering/src/zoom.rs` |
| Editor sidebar | `apps/desktop/src/routes/editor/ConfigSidebar.tsx` |

---

## Planned Features (Screen Studio-inspired)

### Priority 1: Background & Padding
**Status:** 🟡 Partially Done  
**Difficulty:** Easy

Background system exists. Need to add **padding/margin around recording**.

**Already Implemented:**
- [x] Background color picker
- [x] Gradient backgrounds
- [x] Corner radius for recording area

**Needs Implementation:**
- [ ] Padding size slider (px) - Adjust `target_bounds` in compositor
- [ ] Preset themes (dark, light, gradient)

**Implementation Notes:**
- Modify `crates/rendering/src/layers/background.rs` to support inset
- Adjust `target_bounds` calculation in `crates/rendering/src/lib.rs`
- Add padding UI control in `apps/desktop/src/routes/editor/ConfigSidebar.tsx`

---

### Priority 2: Shadow & Inset Effects
**Status:** ✅ Mostly Done  
**Difficulty:** Easy (for remaining work)

**Already Implemented:**
- [x] Drop shadow toggle
- [x] Shadow blur/spread controls
- [x] Shadow color/opacity
- [x] Full shadow UI in `ShadowSettings.tsx`

**Needs Implementation:**
- [ ] Inner shadow/inset option - Minor shader modification

**Implementation Notes:**
- Drop shadow implemented in `composite-video-frame.wgsl` shader
- For inset shadow: Add second shadow pass with inverted alpha mask
- UI already exists, just need to add inset toggle to `ShadowSettings.tsx`

---

### Priority 3: Keyboard Shortcut Display
**Status:** 🔴 Not Started  
**Difficulty:** Medium-Hard

Show keyboard shortcuts on screen as they're pressed during recording.

- [ ] Capture keyboard events during recording (new subsystem)
- [ ] Store key events with timestamps in recording metadata
- [ ] Create new keyboard overlay layer (`layers/keyboard.rs`)
- [ ] Key appearance styling (size, colors, fade duration)
- [ ] Filter for modifier keys only vs all keys
- [ ] Customizable key display position

**Implementation Notes:**
- Needs new capture mechanism in `crates/media/` for keyboard events
- New layer in `crates/rendering/src/layers/keyboard.rs`
- Store events in recording manifest alongside cursor data
- Render as text overlay with configurable styling

---

### Priority 4: Smooth Cursor Movement
**Status:** ✅ Already Implemented  
**Difficulty:** N/A (Done!)

**Already Implemented:**
- [x] Spring-mass-damper physics system (`spring_mass_damper.rs`)
- [x] Cursor interpolation between frames (`cursor_interpolation.rs`)
- [x] Bezier curve smoothing with configurable tension
- [x] Sub-frame interpolation for smoother motion

**Code Location:**
- `crates/rendering/src/spring_mass_damper.rs` - Physics simulation
- `crates/rendering/src/cursor_interpolation.rs` - Frame interpolation

**Note:** May want to expose smoothing intensity to UI if not already available.

---

### Priority 5: Auto-zoom on Cursor
**Status:** ✅ Partially Implemented  
**Difficulty:** Easy (for enhancement)

**Already Implemented:**
- [x] Click-to-zoom markers in timeline
- [x] Auto-detect click events for zoom
- [x] Zoom level controls
- [x] Easing for zoom in/out transitions (spring physics)

**Needs Implementation:**
- [ ] Continuous cursor-following zoom mode (camera follows cursor)
- [ ] Zoom duration controls refinement

**Implementation Notes:**
- Core zoom in `crates/rendering/src/zoom.rs`
- Click detection already exists
- For continuous follow: Update zoom target each frame based on cursor position
- Add "follow cursor" toggle to zoom UI

---

## Summary: What's Actually Left

| Feature | Status | Effort |
|---------|--------|--------|
| Padding around recording | 🟡 Needs work | Easy |
| Inset shadow option | 🟡 Needs work | Easy |
| Keyboard shortcut display | 🔴 Not started | Medium-Hard |
| Continuous cursor-following zoom | 🟡 Enhancement | Easy |
| Smooth cursor | ✅ Done | - |
| Drop shadows | ✅ Done | - |
| Click-based auto-zoom | ✅ Done | - |

**Good news:** 3 of 5 features already implemented! Focus on padding, inset shadows, and keyboard display.

---

## Upstream Sync Strategy

Keep custom changes minimal and isolated to make syncing with upstream easier:
- Monetization removal: ~13 files
- New features: Add in separate modules when possible
- Periodically merge upstream with `git fetch upstream && git merge upstream/main`

---

## Notes

- Using GitHub Issues/Projects on [Cap-Personal](https://github.com/chindris-mihai-alexandru/Cap-Personal) for detailed tracking when needed
- This file is the quick reference for priorities
