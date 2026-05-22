# Creative Instruction Analysis: Imagine Video Engine

**Repository:** `ilyxas/imagine-video-engine`  
**Primary file analysed:** `engine/imagine_scene_generator.yaml`  
**Supporting artefacts:** `tests/sample-test.md`, `examples/example_scenario.txt`, `output/scene_20260522T185013Z.txt`

---

## 1. Overview

The Imagine Video Engine is a sandbox for translating raw narrative ideas—ranging from minimalistic two-word prompts to fully written scene descriptions—into structured, production-ready shot prompts for AI video generators such as Imagine Canvas. Its single instruction file (`engine/imagine_scene_generator.yaml`) functions as the creative brain of the pipeline, converting unfiltered human imagination into dense, renderer-safe token sequences.

This report examines where those instructions enable creative momentum, where they introduce friction, and where targeted adjustments could unlock greater adaptability without sacrificing the clarity that makes the tool useful.

---

## 2. How the Instructions Promote Creative Flow

### 2.1 Radical Input Openness

The most creatively liberating aspect of the instruction set is its input agnosticism. The `context` field (line 2) states:

> *"The user will provide a raw story idea (often in Russian)."*

"Raw story idea" is deliberately undefined. The test cases in `tests/sample-test.md` (lines 3–4, 12–13) demonstrate inputs that are single-sentence vignettes in Russian prose. The `examples/example_scenario.txt` extends this further with a casual, conversational sentence:

> *"Пожилая леди-руководитель ломает компьютерный девайс на рабочем месте из-за технического сбоя. Стиль анимационный трехмерный."*

The engine does not impose any minimum structure on the input. A two-word prompt ("car chase"), a haiku, or a three-paragraph treatment all feed into the same pipeline. This zero-barrier entry is a genuine creative accelerant—it keeps ideation frictionless and lets the engine serve as a rapid-prototyping layer rather than a formal screenplay tool.

### 2.2 The Three-Step Pipeline as a Creative Scaffold

Far from suppressing creativity, the three-step execution path (lines 6–14) functions as a structured creative scaffold that frees the author from having to think about rendering constraints while ideating:

| Step | Creative benefit |
|------|-----------------|
| **Step 1 – Asset Isolation** (line 9–10) | Forces the creator to commit to concrete visual characters and objects, grounding abstract ideas into renderable entities. |
| **Step 2 – Cinematic Layout** (line 11–12) | Translates story beats into a shot list, which is the natural unit of visual storytelling. Cinematic vocabulary (dolly-in, whip pan, anamorphic) is embedded automatically, giving even non-technical users professional-grade framing. |
| **Step 3 – Token Translation** (line 13–14) | Handles the "last mile" translation to machine-readable tokens, sparing the author from having to learn prompt engineering syntax. |

The output in `output/scene_20260522T185013Z.txt` (7 shots) demonstrates this pipeline in action: a casual scenario becomes a coherent cinematic sequence with consistent subject IDs (`[SUBJECT-01]`), camera specs (`35mm anamorphic`, `dolly-in`), and atmospheric tags—all without the user ever specifying any of those details.

### 2.3 Aesthetic Target as a Creative Dial

The `structure_requirement` pattern (line 18) includes:

> *"[Target Aesthetic, e.g., High-End 3D Pixar Animation or 35mm Photorealistic Cinema]"*

This open-ended field is a subtle but powerful creative lever. A single input can be redirected toward entirely different visual worlds by changing the aesthetic target, making the engine reusable across wildly different creative projects without altering the rest of the instruction set.

---

## 3. Where the Instructions Constrain Creative Flow

### 3.1 The "Anti-Creative" Routing Rule

The most significant constraint is the `critical_routing_rule` (lines 3–5):

> *"Do not engage in creative storytelling. Act strictly as a Quality Control compiler."*

While this rule protects output quality and prevents the model from drifting into conversational filler, it also explicitly closes the door on generative creative assistance. The engine cannot suggest alternative narrative angles, propose a different emotional arc for a scene, or flag when an input idea has underexplored potential. For a sandbox oriented toward idea exploration, this is a meaningful limitation: the tool processes ideas but never extends them.

### 3.2 Stripping Metaphors and Atmospheric Language

Step 3 (line 14) instructs the engine to:

> *"Remove all narrative words, metaphors, and descriptions that cannot be rendered as pixels."*

This rule is technically sound—AI video generators respond to visual tokens, not abstract concepts. However, it creates a lossy translation for ideas that are emotionally or atmospherically driven. A prompt like "a sense of melancholy at dusk" carries creative intent that the current pipeline would strip down to `sunset, warm orange light, soft shadows, slow camera pan`—accurate, but flattened. Mood, subtext, and emotional register are not preserved in the output unless they happen to be visually literal.

### 3.3 Fixed Shot Duration

The shot template (lines 25–26) hard-codes a `3-5s` duration for every shot:

```yaml
- "Shot 1 (3-5s): [Verified Prompt Tokens]"
- "Shot 2 (3-5s): [Verified Prompt Tokens]"
```

This is a sensible default for Imagine Canvas's typical clip length, but it is not exposed as a variable. A montage might need 1-second cuts; a contemplative long-take might need 10 seconds. The instruction set provides no path for overriding this per-shot or per-project.

### 3.4 Rigid ID System with No Continuity Evolution

The `structure_requirement` (line 18) mandates:

> *"Do not repeat physical descriptions once the ID is defined."*

The Unique Textual ID system (`[SUBJECT-01]`, `[PROP-02]`) is elegant for consistency, but it treats characters as static objects. There is no mechanism to describe a character changing appearance across shots (torn clothing, new prop in hand, emotional shift expressed through body language) without re-introducing a full description. The constraint optimises for renderer deduplication at the cost of character evolution.

### 3.5 Shot Count Is Unbounded but Implicit

The instruction set specifies camera specs and shot sequencing but never defines a target shot count for a given input length. The example output in `output/scene_20260522T185013Z.txt` produces 7 shots from a single sentence. For short prompts, this may be over-expansive; for rich scenarios, it may truncate the story. There is no signal to the engine about desired sequence length.

---

## 4. Areas Where the Sandbox Thrives

### 4.1 Rapid Idea-to-Shot-List Conversion

The engine's clearest strength is speed. The pipeline collapses the distance between "I have an idea" and "I have a camera-ready prompt sequence" to a single invocation. The `final_output_requirement` (lines 19–21) enforces clean, copy-pasteable output with zero preamble—a deliberate UX choice that keeps the creative loop tight.

### 4.2 Language-Agnostic Input Processing

Accepting Russian-language input (referenced in lines 2, 4) and producing English-token output is a non-trivial feature that makes the engine accessible to non-English-speaking creators. The test cases in `tests/sample-test.md` validate this pattern. This is one of the most quietly adaptive aspects of the instruction set.

### 4.3 Implicit Professional Cinematography Knowledge

By embedding cinematic vocabulary (anamorphic lenses, dolly movements, lighting terminology) into Step 2 (line 12), the engine democratises production-level shot design. A creator with no film school background can produce technically coherent shot descriptions. This is the sandbox's most compelling creative accelerant.

---

## 5. Opportunities to Enhance Flexibility and Adaptability

### 5.1 Introduce an Optional Creative Expansion Mode

**Current gap:** The `critical_routing_rule` (line 5) prohibits any generative or narrative input from the engine.  
**Proposed change:** Add an optional `creative_expansion: true/false` flag (defaulting to `false`). When enabled, the engine could offer one alternative interpretation of the input before proceeding to pipeline execution—for example, suggesting a different genre framing or a reversed narrative structure. This preserves the default strict mode while unlocking a creative co-pilot mode for exploratory sessions.

### 5.2 Parameterise Shot Duration

**Current gap:** Shot duration is hard-coded as `3-5s` in the example template (lines 25–26).  
**Proposed change:** Expose a `shot_duration` field with sensible defaults (`short: 1-2s`, `standard: 3-5s`, `long: 6-10s`) that the user can set per scene or per shot. This would make the engine useful for music video editing (short cuts) as well as contemplative narrative work (long takes).

### 5.3 Allow Mood/Atmosphere Tokens to Survive Step 3

**Current gap:** The Step 3 instruction (line 14) strips metaphors and non-pixel-renderable language indiscriminately.  
**Proposed change:** Introduce a `preserve_mood: true` mode that retains abstract emotional qualifiers as a dedicated token class (e.g., `[MOOD: melancholic]`, `[TONE: tense]`) passed alongside the visual tokens. Many AI video generators support style and mood tokens alongside visual prompts; preserving this layer would retain creative intent across the full pipeline.

### 5.4 Support Character State Evolution

**Current gap:** The ID system (line 18) forbids re-describing a character once their ID is set.  
**Proposed change:** Allow per-shot `state_delta` annotations—small additive descriptors applied to an ID without repeating its full definition. Example: `[SUBJECT-01 + torn jacket sleeve, dust on shoes]`. This would let the pipeline model character arcs and physical consequences across a sequence.

### 5.5 Signal Desired Sequence Length

**Current gap:** There is no instruction governing how many shots to generate for a given input.  
**Proposed change:** Add an optional `target_shots` parameter to guide output length. For tightly constrained platforms (e.g., 3-shot reels), the engine could compress the narrative; for longer projects, it could expand. A default of `auto` would preserve current behaviour.

---

## 6. Summary

| Dimension | Current State | Opportunity |
|---|---|---|
| Input flexibility | ✅ Excellent — any language, any length | No change needed |
| Output structure | ✅ Clean and copy-pasteable | No change needed |
| Shot duration | ⚠️ Fixed at 3-5s | Parameterise |
| Emotional/mood fidelity | ⚠️ Stripped in Step 3 | Preserve as optional token class |
| Character continuity | ⚠️ Static IDs only | Allow state deltas |
| Creative suggestion | ❌ Explicitly forbidden | Add optional expansion mode |
| Sequence length control | ❌ Implicit/uncontrolled | Add `target_shots` parameter |

The Imagine Video Engine's instruction set is already well-calibrated for its core purpose: fast, no-fuss conversion of raw ideas into renderer-ready shot sequences. Its greatest creative strengths—language agnosticism, embedded cinematography knowledge, and zero-friction input—are real and should be preserved. The friction points identified above are all additive opportunities rather than fundamental redesigns: small, toggleable extensions that would expand the engine's range without disrupting its current workflow.

The engine thrives precisely because it does not demand formalism from its users. Future iterations should carry that philosophy forward into the instruction layer itself.
