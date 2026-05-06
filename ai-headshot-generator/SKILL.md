---
name: ai-headshot-generator
description: Generate high-quality AI headshots from personal reference photos and style references using a multi-step vision-verified pipeline. Use when asked to create headshots, portraits, profile photos, or AI-generated photos of a person in specific styles. Triggers on "headshot", "portrait", "profile photo", "generate a photo of me", or "AI photo".
---

# AI Headshot Generator

A rigorous multi-step pipeline that generates polished AI headshots matching a person's identity to a target style. Uses vision verification at every step to ensure quality — the same process a human art director would use.

## Model

This skill uses **GPT Image 2** (`gpt-image-1` generation and edit endpoints). This is the only model that produces the required quality for this pipeline. The user may provide access through any provider that supports it:

| Provider | Key env var | Generation model ID | Edit model ID | Setup |
|----------|-------------|---------------------|---------------|-------|
| **fal.ai** (default, recommended) | `FAL_KEY` | `openai/gpt-image-2` | `openai/gpt-image-2/edit` | `npm install @fal-ai/client` |
| **OpenAI direct** | `OPENAI_API_KEY` | `gpt-image-1` | `gpt-image-1` (edit endpoint) | `npm install openai` |
| **OpenRouter** | `OPENROUTER_API_KEY` | `openai/gpt-image-1` | `openai/gpt-image-1` | OpenAI SDK with `baseURL: https://openrouter.ai/api/v1` |

**Ask the user which key they have.** If they don't specify, default to fal.ai — it's the simplest setup and supports both generation and edit in one client. Adapt the API calls in Phases 2–3 to match the provider, but the prompts, pipeline, and verification are identical regardless of provider.

### Provider Examples

<details>
<summary>fal.ai (default)</summary>

```javascript
import { fal } from "@fal-ai/client";
fal.config({ credentials: process.env.FAL_KEY });

// Generation
const result = await fal.subscribe("openai/gpt-image-2", {
  input: { prompt, image_url: styleUrl, image_size: { width: 3072, height: 3072 }, quality: "high", num_images: 1, output_format: "png" },
});
const url = result.data?.images?.[0]?.url ?? result.images?.[0]?.url;

// Edit (multiple image refs)
const result = await fal.subscribe("openai/gpt-image-2/edit", {
  input: { prompt, image_urls: [...identityUrls, styleUrl, explorationUrl], image_size: { width: 3072, height: 3072 }, quality: "high", num_images: 1, output_format: "png" },
});
```
</details>

<details>
<summary>OpenAI direct</summary>

```javascript
import OpenAI, { toFile } from "openai";
import { readFile } from "node:fs/promises";
const openai = new OpenAI(); // uses OPENAI_API_KEY

// Generation
const result = await openai.images.generate({
  model: "gpt-image-1", prompt, size: "1024x1024", quality: "high", n: 1,
});
const b64 = result.data[0].b64_json;

// Edit (multiple image refs — pass as array of Files)
const images = await Promise.all(
  imagePaths.map(p => readFile(p).then(buf => toFile(buf, path.basename(p))))
);
const result = await openai.images.edit({
  model: "gpt-image-1", prompt, image: images, size: "1024x1024", quality: "high", n: 1,
});
```
</details>

<details>
<summary>OpenRouter</summary>

```javascript
import OpenAI from "openai";
const client = new OpenAI({
  apiKey: process.env.OPENROUTER_API_KEY,
  baseURL: "https://openrouter.ai/api/v1",
});
// Same as OpenAI direct, but model: "openai/gpt-image-1"
```
</details>

## What to Ask the User For

Before starting, guide the user to provide two things:

### 1. Identity references (5–10 photos of themselves)
Ask: *"Give me 5–10 photos of yourself — different angles, different lighting, different days if possible. The more variety, the better I can preserve your likeness. Drop them in a `personal-references/` folder."*

Good references include:
- Front-facing, three-quarter, and profile angles
- Different lighting conditions (indoor, outdoor, natural, flash)
- Clear shots of the face (no sunglasses, minimal obstruction)
- A mix of casual and put-together — the model needs to know what you actually look like, not just one flattering angle

### 2. Style references (1 image per headshot they want)
Ask: *"Now give me a reference image for each style of headshot you want. Find a portrait you like the look of — the lighting, colors, mood, background — and drop it in `style-references/`. I'll generate a headshot of you that matches each style."*

Each style reference = one headshot output. If they give 5 style references, they get 5 headshots. If they give 1, they get 1. The user does NOT need to describe the style in words — you will visually analyze each reference image yourself in Phase 1.

## Requirements

- **API key** for one of the providers above
- **Personal reference photos** (5–10 images of the subject from different angles)
- **Style reference images** (1 per desired headshot output)

## The Pipeline

This is the exact process to follow. Do not skip steps. Do not shortcut the verification.

### Phase 0: Setup

1. Create a project directory with this structure:
```
project/
├── .env                    # FAL_KEY=... (or OPENAI_API_KEY, OPENROUTER_API_KEY)
├── personal-references/    # Subject's photos
├── style-references/       # Target style images
├── outputs/
│   ├── style-explorations/ # Step 1 outputs
│   ├── candidates/         # Step 2 outputs
│   └── finals/             # Verified final outputs
└── data/
    └── styles.json         # Style metadata (auto-generated)
```

2. Install the appropriate client for the user's provider (see Model section above).

### Phase 1: Analyze References

**Visually inspect every personal reference photo** using the `read` tool. Build a mental model of the subject's distinguishing features:
- Face shape, bone structure
- Skin tone
- Eye color and shape
- Hair color, style, length, texture
- Facial hair (or lack thereof)
- Age range
- Any distinctive features (dimples, freckles, moles, etc.)

Write this down as an `IDENTITY_DESCRIPTION` — be extremely specific. This is the anchor for all verification later.

**Visually inspect every style reference photo.** For each, write:
- `note`: One-sentence summary of what makes this style distinctive
- `prompt`: Detailed comma-separated description covering: lighting direction and quality, color palette/temperature, background, wardrobe, camera angle/crop, mood/expression, film treatment/texture

Save these to `data/styles.json`:
```json
[
  {
    "id": "style-01",
    "file": "style-references/style-01.jpeg",
    "note": "Warm ochre studio portrait with hard low side light...",
    "prompt": "warm ochre editorial studio portrait, hard low side sunlight from camera left, deep sculptural shadows..."
  }
]
```

### Phase 2: Style Exploration (Step 1 of generation)

For each style, generate a **style exploration** — a test image that nails the aesthetic using a generic subject. This is NOT the final output. It exists to give the edit model a concrete visual target.

Use the generation endpoint for the user's provider (see Model section). The prompt:

```
Create a reference-quality editorial headshot style study, not the final identity output.
Visual direction: ${style.prompt}.
Square 1:1 aspect ratio portrait crop.
Use a generic adult subject only as a placeholder.
Focus on lighting, crop, background, wardrobe mood, camera treatment, and color.
No text, no watermark.
```

Pass the style reference image as input so the model can see what it's matching.

Settings: 3072×3072 (or max supported size), high quality, PNG.

**Verify with vision:** Read the style exploration output and compare against the style reference. Check:
- Does the lighting direction match?
- Does the color palette match?
- Does the background match?
- Does the overall mood match?

If it's significantly off, regenerate with an adjusted prompt. Move on once the style is captured.

### Phase 3: Identity Headshot (Step 2 of generation)

Feed the edit endpoint ALL references — identity photos + style reference + style exploration. The prompt:

```
Create a high-resolution realistic editorial headshot of the exact same person shown in the identity reference images.
Preserve identity carefully: ${IDENTITY_DESCRIPTION}.
Do not age the subject, do not add facial hair beyond what's in the references, do not change eye color, do not change hair color or style, do not make them look like the people in the style reference.
Match this visual style: ${style.prompt}.
Square 1:1 aspect ratio portrait crop.
Polished headshot suitable for a personal portfolio. Natural realistic skin texture, no text, no watermark, no extra people, no sunglasses, no earbuds, no phone.
```

Pass all images (identity refs + style ref + exploration) to the edit endpoint.

Settings: 3072×3072 (or max supported size), high quality, PNG.

### Phase 4: Vision Verification (Critical)

This is what makes the pipeline reliable. For EACH candidate:

**Identity check:** Read the candidate output alongside 2–3 of the personal reference photos. Ask yourself:
- Does this look like the same person?
- Are the eyes the right color?
- Is the hair the right color, style, and length?
- Is the face shape correct?
- Is the age approximately right?
- Are there any added features (glasses, beard, earrings) that don't belong?

**Style check:** Read the candidate output alongside the style reference. Ask yourself:
- Does the lighting match?
- Does the color palette match?
- Does the background match?
- Does the crop/framing match?
- Does the overall mood match?

**Quality check:**
- Is the image at least 2880px on the longest side?
- Is it free of artifacts, extra limbs, text, watermarks?
- Does it look like a real photograph, not AI-generated?

**If identity fails:** Regenerate with stronger identity language in the prompt. Add more specific descriptions of what went wrong ("the subject should have BLUE eyes, not brown").

**If style fails:** Regenerate the style exploration first, then re-run the headshot.

**If quality fails:** Regenerate.

Only move a candidate to `outputs/finals/` when it passes ALL three checks. Maximum 3 retries per style before flagging to the user.

### Phase 5: Parallel Execution

When processing multiple styles, run them in parallel (5 concurrent) to avoid waiting hours:

```javascript
async function runParallel(tasks, concurrency = 5) {
  const results = [];
  let idx = 0;
  async function worker() {
    while (idx < tasks.length) {
      const i = idx++;
      try {
        results[i] = { ok: true, value: await tasks[i]() };
      } catch (err) {
        results[i] = { ok: false, error: err };
      }
    }
  }
  await Promise.all(
    Array.from({ length: Math.min(concurrency, tasks.length) }, worker)
  );
  return results;
}
```

Each parallel task runs the full pipeline for one style: explore → generate → verify → retry if needed.

### Phase 6: Gallery Output

Once all styles have verified finals, build a lightbox HTML gallery:

- Grid of final headshot thumbnails
- Click-to-lightbox full-size view
- Style reference thumbnail + notes under each
- Download links
- QA status (identity ✓, style ✓, resolution ✓)

Open the gallery in a browser for the user to review.

## Key Principles

1. **Never skip vision verification.** The model generates good results ~70% of the time. Verification + retry is what gets you to ~95%.
2. **The two-step generation is essential.** Style exploration → identity edit produces dramatically better results than a single generation call. The exploration gives the edit model a concrete visual target.
3. **Be specific in identity descriptions.** Vague prompts like "preserve the person's appearance" fail. Specific prompts like "pale skin, blue eyes, dark medium-length center-parted hair, slim face, straight nose, clean-shaven, youthful early-to-mid 20s" work.
4. **Upload all identity refs to every edit call.** More references = better identity preservation. Use 3–6 of the best photos.
5. **Always use 1:1 square aspect ratio** unless the user requests otherwise. It's the most versatile crop for headshots.
6. **Retry with adjusted prompts, not the same prompt.** If the identity is wrong, add corrective language. If the style is wrong, regenerate the exploration.

## Prompt Templates

### Identity Description Template
```
[skin tone] skin, [eye color] eyes, [hair description including color/style/length],
[face shape] face, [nose description], [facial hair status],
[age range] appearance, [any distinctive features]
```

### Style Prompt Template
```
[overall style], [lighting direction and quality], [color palette/temperature],
[background description], [wardrobe/clothing], [camera angle and crop],
[expression/mood], [film treatment/texture]
```
