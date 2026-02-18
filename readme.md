
For Even More detail understanding, Go to `Guide to generate Real looking Ai images.md`(its in files above)
# The Complete Technical Dossier: Photorealistic AI Influencer Pipeline

> **Ethics Note:** Many platforms and jurisdictions now require disclosure of AI-generated content. Virtual influencers are a legitimate industry (Lil Miquela, Aitana Lopez, etc.), but transparency with your audience is both ethical and increasingly a legal obligation. Build a brand — don't build a lie.

---

## PHASE 0: UNDERSTANDING WHY AI IMAGES LOOK FAKE

Before fixing anything, you need to understand the **seven deadly tells** that scream "AI":

| Tell | Why It Happens | What Humans Actually Look Like |
|---|---|---|
| **The Glow** | Diffusion models default to even, soft lighting across the entire face | Real faces have harsh shadows, uneven skin tone zones, redness patches |
| **The Symmetry** | Models converge toward mathematical averages | Real faces have one eye slightly higher, one nostril wider, uneven jawline |
| **The Skin** | Models produce smooth gradient skin like a foundation ad | Real skin has visible pores, peach fuzz catching sidelight, texture variation across zones (oily T-zone, dry cheeks) |
| **The Eyes** | Perfect catchlights, identical iris detail, eerily sharp | Real eyes have slightly different reflections per eye, visible veins in sclera, moisture variance |
| **The Expression** | "Smiling for the camera" default | Real humans in candid shots have transitional micro-expressions, tension asymmetry, dead zones |
| **The Hair** | Smooth flowing mass, every strand placed | Real hair has flyaways, frizz, grease patches, inconsistent curl patterns |
| **The Environment Disconnect** | Subject looks composited onto the background | Real photos have consistent depth-of-field falloff, ambient light color cast on skin, environmental reflections in eyes |

**Your entire workflow exists to systematically destroy each of these tells.**

---

## PHASE 1: ENGINE & MODEL SELECTION

### Primary Platform: **ComfyUI** (Non-negotiable)

Not Automatic1111. ComfyUI gives you node-level granular control over every single step of the pipeline. You need this control.

**Install:**
- ComfyUI base
- ComfyUI Manager (for easy node installation)
- All ControlNet auxiliary nodes
- IPAdapter Plus nodes
- FaceDetailer / Impact Pack nodes
- UltralyticsDetector nodes

### Base Checkpoint Models (Ranked by Photorealism)

**For SDXL (recommended primary pipeline):**

| Model | Strength | Use Case |
|---|---|---|
| **JuggernautXL v9** | Best overall photorealism, natural skin | Primary workhorse |
| **RealVisXL V5.0** | Excellent natural lighting response | Outdoor/candid shots |
| **Zavychroma XL** | Strong ethnic diversity, non-Western defaults | Avoids "Euro-default face" |
| **CopaxTimelessXL** | Cinematic feel without over-stylization | Lifestyle/editorial shots |

**For FLUX (bleeding edge):**

| Model | Strength | Use Case |
|---|---|---|
| **FLUX.1 [dev]** | Exceptional text understanding, very photorealistic | Complex scene composition |
| **FLUX Realism LoRA** (by XLabs) | Pushes FLUX toward raw photo feel | All use cases |

**For SD 1.5 (if your GPU is limited, 4-6GB VRAM):**

| Model | Strength |
|---|---|
| **CyberRealistic V4.2** | Best 1.5-based photorealism |
| **Photon V1** | Natural lighting, good skin |
| **RealisticVision V6** | Solid all-rounder |

> **GPU Requirement:** SDXL pipeline = minimum 8GB VRAM (12GB+ recommended). FLUX = 12GB minimum (24GB ideal). SD1.5 = 4GB+.

---

## PHASE 2: THE LORA ARSENAL

LoRAs are small fine-tuned weight files that steer the model toward specific aesthetics. You need a curated stack.

### Mandatory LoRA Categories:

**1. Anti-AI / Raw Photo LoRAs** (These are your most critical weapons)

| LoRA Name (CivitAI) | Weight | Purpose |
|---|---|---|
| **Raw Photo Style** | 0.4–0.6 | Strips studio perfection, adds camera sensor noise character |
| **Detailed Skin Texture** | 0.3–0.5 | Injects pore-level detail, peach fuzz, uneven texture |
| **FilmGrain / Analog** | 0.2–0.4 | Adds sensor noise, slight color channel separation |
| **Bad Lighting LoRA** | 0.3–0.5 | Trains on photos with genuinely mediocre lighting |

**2. Face Authenticity LoRAs**

| LoRA | Weight | Purpose |
|---|---|---|
| **Asymmetric Face** | 0.2–0.35 | Breaks the symmetry default |
| **South Asian / East Asian / African / MENA feature LoRAs** | 0.4–0.7 | Overrides Western-default facial structure based on your desired ethnicity |
| **Subtle Expression** | 0.3–0.5 | Mutes exaggerated expressions toward micro-expression territory |

**3. Body Realism LoRAs**

| LoRA | Weight | Purpose |
|---|---|---|
| **Realistic Body Proportions** | 0.4–0.6 | Counters the exaggeration bias |
| **Natural Posture** | 0.3–0.5 | Introduces authentic weight distribution, slouch, off-axis lean |

**4. Environment / Lighting LoRAs**

| LoRA | Weight | Purpose |
|---|---|---|
| **iPhone / Samsung Camera Style** | 0.5–0.7 | Mimics phone camera processing (crucial for "candid" shots) |
| **DSLR specific (Canon EOS R5, Sony A7III)** | 0.4–0.6 | For "professional" shoot images |
| **Indoor Ambient Light** | 0.3–0.5 | Fluorescent, tungsten, mixed-source lighting |

### LoRA Stacking Rules:
- **Never exceed ~1.5 total combined weight** across all LoRAs or you get artifacts
- Always test LoRAs individually before stacking
- Prioritize Anti-AI + Skin Texture + Camera Style as your core three

---

## PHASE 3: THE PROMPT ARCHITECTURE

This is where most people fail catastrophically. They write "beautiful woman taking a selfie" and wonder why it looks fake.

### The Prompt Formula:

```
[CAMERA HARDWARE] + [TECHNICAL SETTINGS] + [LIGHTING ENVIRONMENT] + 
[SUBJECT DESCRIPTION - PHYSICAL] + [SUBJECT DESCRIPTION - EXPRESSION/MOOD] + 
[CLOTHING/STYLING] + [ACTION/POSE] + [ENVIRONMENTAL CONTEXT] + [FILM/PROCESSING STYLE]
```

### Component Breakdown with Examples:

**CAMERA HARDWARE** (Specify exact models — the model knows these signatures):
```
shot on iPhone 13 Pro
shot on Canon EOS R5 with 85mm f/1.4 lens
shot on Samsung Galaxy S23 Ultra
Fujifilm X-T4, 35mm f/1.4
Google Pixel 7 rear camera
```

**TECHNICAL SETTINGS:**
```
f/1.8 aperture, shallow depth of field, slight motion blur
f/8, deep focus, 1/250 shutter speed
ISO 3200, visible sensor noise, slight grain
slightly overexposed highlights, crushed shadows
white balance slightly warm, 5800K
```

**LIGHTING ENVIRONMENT** (This is where you kill the AI glow):

For Candid/Natural:
```
lit by single overhead fluorescent tube, greenish color cast on skin
harsh midday sun directly overhead, deep eye socket shadows, squinting
golden hour from camera-left at 30 degrees, long shadows, warm highlights with cool shadow fill
backlit by window, face in partial shadow, rim light on hair edges
lit by phone screen in dark room, bluish underlight on face
unflattering gas station lighting, mixed sodium vapor and fluorescent
overcast flat lighting, no directional shadows, muted colors
```

For Professional/Ad:
```
single large softbox camera-right at 45 degrees, subtle fill card camera-left, slight shadow under jawline
two-point lighting setup, key light 3/4 position, hair light from behind, no fill (dramatic)
natural window light studio, large north-facing window, white bounce card for fill
ring light with visible catchlight in both eyes (influencer-style but not overdone)
```

**SUBJECT DESCRIPTION — PHYSICAL:**

> ⚠️ CRITICAL: Never say "beautiful." Never say "attractive." Describe SPECIFIC features.

```
23 year old South Asian woman, medium-brown skin with warm olive 
undertone, visible skin texture on cheeks, slight hyperpigmentation 
near temples, naturally thick eyebrows slightly uneven, dark brown 
eyes with visible limbal ring, small nose with rounded tip, full 
lips with natural color variation (slightly darker lower lip), 
oval face with soft jawline, black hair with natural wave pattern 
and visible flyaways, slight dark circles under eyes, 
a few tiny moles on left cheek and neck, ears slightly 
different sizes, peach fuzz visible on upper lip and jawline 
catching the sidelight
```

```
26 year old Middle Eastern woman, light olive skin, visible pores 
on nose and inner cheeks, natural brow shape (not threaded to 
perfection), hazel-green eyes with slight heterochromia in left eye, 
aquiline nose with subtle bump on bridge, medium lips with natural 
dryness on lower lip, angular face with high cheekbones, 
dark brown hair pulled back messily with loose strands, 
tiny scar on chin, natural ear shape with attached lobes, 
no visible makeup or very minimal tinted moisturizer
```

**Key descriptors to always include:**
- `visible skin pores`
- `peach fuzz on face catching light`
- `slight skin texture variation`
- `natural eyebrows (not perfectly shaped)`
- `visible flyaway hairs`
- `subtle asymmetry in facial features`
- `natural lip color and texture`
- `realistic eye moisture and veining in sclera`
- `no airbrushed skin`

**SUBJECT DESCRIPTION — EXPRESSION/MOOD:**

> ⚠️ NEVER USE: smile, happy, looking at camera seductively, pouting

Instead:

```
CANDID: caught mid-thought, eyes focused on something off-frame to 
the right, mouth slightly parted, neutral resting expression, 
slight tension in the jaw

CANDID: laughing genuinely with eyes crinkled (crow's feet engaging), 
mouth open showing slight tooth misalignment, head tilted back 5 degrees

SELFIE: the exact expression of someone checking their phone camera 
angle, slight concentration furrow between brows, lips in neutral 
position, direct eye contact with camera lens

PROFESSIONAL: composed neutral expression with quiet confidence, 
direct gaze, jaw relaxed, subtle Mona Lisa tension at corner 
of mouth, not smiling

LIFESTYLE: looking down at coffee cup, profile view, relaxed 
facial muscles, slight double chin visible from angle (realistic)
```

**CLOTHING / STYLING:**

```
wearing a slightly wrinkled oversized beige linen shirt, 
top two buttons undone, sleeves rolled unevenly, fabric draping 
naturally with visible creases from sitting

wearing a fitted black ribbed tank top, slight pilling on fabric, 
natural body shape visible without exaggeration, bra strap 
slightly visible on left shoulder
```

**ACTION / POSE:**

> ⚠️ NEVER: "posing for camera" (unless specifically a professional shoot)

```
CANDID: leaning against a weathered concrete wall, weight on left 
foot, right foot crossed over, arms loosely crossed, phone in 
right hand, head turned 15 degrees to the left

SELFIE: arm extended holding phone (slight wide-angle distortion on 
near hand), other hand touching hair absently, shot from slightly 
above at 15 degree angle

PROFESSIONAL: seated on a wooden stool, spine straight but not rigid, 
hands resting naturally on thighs, ankles crossed

LIFESTYLE: sitting cross-legged on an unmade bed, laptop open in 
front, hunched slightly forward, hair tucked behind one ear
```

**ENVIRONMENTAL CONTEXT:**

```
in a slightly messy studio apartment, warm-toned walls, random 
objects on shelves in background (blurred), afternoon light 
through dusty blinds casting stripe shadows

on a busy sidewalk in an urban area, other pedestrians blurred 
in background, concrete and signage visible, slightly dirty 
street environment

in a mid-range cafe, wooden table with water glass ring stains, 
other patrons blurred in background, warm interior lighting 
mixed with daylight from window
```

**FILM / PROCESSING STYLE:**

```
raw unedited photo feel, no color grading, realistic dynamic range
slightly edited with VSCO-style filter, subtle fade in blacks, 
slightly desaturated
iPhone photo with default processing, slight HDR look
professional photo with minimal retouching, natural color science
slight lens distortion at edges, natural vignetting
```

---

## PHASE 4: THE NEGATIVE PROMPT (Your Defensive Shield)

### Structured Negative Prompt (for SDXL):

```
(worst quality:1.4), (low quality:1.4), (normal quality:1.2),

(airbrushed skin:1.6), (smooth skin:1.4), (plastic skin:1.5), 
(poreless skin:1.5), (waxy skin:1.4), (shiny skin:1.3), 
(glowing skin:1.5), (luminous skin:1.4), (flawless skin:1.5),

(symmetrical face:1.3), (perfect face:1.4), (doll face:1.5), 
(baby face:1.2), (uncanny valley:1.4), (AI generated:1.3),

(perfect teeth:1.3), (fake smile:1.5), (exaggerated smile:1.4), 
(acting:1.4), (posed expression:1.3), (modeling expression:1.3),

(studio lighting:1.2), (rim lighting:1.2), (dramatic lighting:1.2), 
(perfect lighting:1.4), (glamour lighting:1.5), (bollywood lighting:1.5), 
(golden glow:1.3),

(cartoon:1.8), (anime:1.8), (hentai:1.8), (3d render:1.6), 
(cgi:1.5), (illustration:1.6), (digital art:1.5), (painting:1.5),

(exaggerated proportions:1.6), (huge breasts:1.7), (tiny waist:1.5), 
(unrealistic body:1.5), (plastic surgery look:1.5), 
(silicon implants:1.5), (inflated lips:1.4),

(heavy makeup:1.4), (instagram makeup:1.3), (contoured face:1.3), 
(overdone eyebrows:1.4), (false eyelashes:1.3),

(oversaturated:1.3), (HDR:1.2), (over-sharpened:1.3), 
(over-processed:1.3), (beauty filter:1.5),

(deformed:1.4), (disfigured:1.3), (extra fingers:1.5), 
(mutated hands:1.5), (bad anatomy:1.3), (bad hands:1.4), 
(missing fingers:1.4), (extra limbs:1.5), (fused fingers:1.4),

(text:1.3), (watermark:1.5), (signature:1.3), (border:1.2), 
(frame:1.2)
```

### Negative Prompt Weighting Rules:
- **1.0** = Normal negative strength
- **1.3–1.5** = Strong avoidance (use for your core "kills": AI glow, fake skin, etc.)
- **1.6–1.8** = Nuclear — will aggressively steer away (use for cartoon/anime/hentai)
- **>2.0** = Avoid — causes artifacts and color distortion

---

## PHASE 5: CONTROLNET CONFIGURATION (The Skeleton)

ControlNet is how you enforce physical reality onto the generation.

### Essential ControlNet Models to Install:

| Model | Purpose | When to Use |
|---|---|---|
| **OpenPose** | Controls body pose, hand position, face direction | Every generation with a specific pose in mind |
| **Depth (MiDaS/Zoe)** | Controls spatial depth relationships | Scene compositions, preventing floating/composited look |
| **Canny Edge** | Preserves edge structure from reference | When matching a specific composition |
| **IP-Adapter FaceID Plus V2** | **CRITICAL** — maintains face identity across images | Every single generation after establishing your character |
| **InstantID** | Alternative face consistency (works with SDXL) | Can combine with IP-Adapter |
| **Tile** | Upscaling while preserving details | Final upscale pass |

### ControlNet Settings for Each Use Case:

**IP-Adapter FaceID (Face Consistency):**
```
Preprocessor: ip-adapter_face_id_plus
Model: ip-adapter-faceid-plusv2_sdxl.bin
Weight: 0.7 - 0.85 (not higher or you lose variation/naturalness)
Start: 0.0
End: 0.85
```

**OpenPose (Pose Control):**
```
Preprocessor: openpose_full (includes hands and face landmarks)
Weight: 0.55 - 0.75
Start: 0.0
End: 0.8
```
> Use reference photos of REAL people in the pose you want. Take them yourself or source from stock. Feed into OpenPose.

**Depth:**
```
Preprocessor: depth_zoe
Weight: 0.4 - 0.6
Start: 0.0
End: 0.7
```

---

## PHASE 6: THE FACE CONSISTENCY PIPELINE (Creating Your "Person")

This is the single most critical technical challenge: making the same fictional person appear consistently across 100+ images.

### Step-by-Step Face Lock Protocol:

**Step 1: Generate the "Genesis Image"**

Create your character's definitive face. Generate 50-100 images with your prompt until you find THE face. This is your "character sheet" source.

Key settings for genesis:
```
Steps: 35-50 (higher for more detail convergence)
CFG: 5-7 (SDXL) / 3.5-4.5 (FLUX)
Sampler: DPM++ 2M Karras (SDXL) / Euler (FLUX)
Resolution: 1024x1024 (SDXL native) / 1024x1024+ (FLUX)
```

**Step 2: Create Reference Image Set**

From your genesis image, use Inpainting + ADetailer to refine. Then generate 6-10 variations of this face:
- Front facing, neutral expression, even lighting
- 3/4 view left
- 3/4 view right
- Profile left
- Slight smile variation
- Looking down / up angles

**Step 3: Train a LoRA on Your Character (Optional but Powerful)**

Using Kohya_ss or similar:
- Collect your 10-20 best generated images of the character
- Caption each carefully
- Train a LoRA at a low learning rate (1e-4 to 5e-5)
- 500-1500 steps typically sufficient
- Result: A dedicated LoRA that you load at weight 0.6-0.8 to invoke your character

**Step 4: IP-Adapter FaceID for Every Generation**

For every new image you generate:
1. Load your best front-facing genesis image into IP-Adapter FaceID
2. Set weight to 0.7-0.85
3. Combine with your character LoRA at 0.5-0.7
4. This double-lock gives you 85-95%+ face consistency

**Step 5: ADetailer Post-Pass**

After every generation, run ADetailer (After Detailer):
```
Model: face_yolov8n.pt
Prompt: (repeat your facial feature description)
Negative: (repeat your negative prompt)
Denoising: 0.25 - 0.4
Inpaint area: Only masked
```
This fixes any minor face consistency drift.

---

## PHASE 7: SPECIFIC SCENARIO WORKFLOWS

### Scenario A: Candid Selfie

```
PROMPT:
raw photo, shot on iPhone 14 Pro front camera, 12MP, 
slight wide-angle lens distortion, f/1.9 aperture,

[YOUR CHARACTER DESCRIPTION],

taking a selfie with right arm extended (arm partially visible 
at bottom edge of frame), in a bathroom mirror background 
slightly visible and out of focus, messy hair loosely tied back, 
wearing an old oversized grey t-shirt with small stain near collar, 
no makeup, natural skin with slight oiliness on forehead, 
expression of someone quickly checking their appearance - 
neutral with slight concentration between brows, 
direct eye contact with phone camera,

bathroom has yellowish tungsten lighting from overhead fixture, 
unflattering downward light creating slight shadow under nose 
and chin, warm color temperature, visible noise/grain consistent 
with indoor phone photo, slightly out of focus on extended hand,

no filter, no editing, raw phone capture quality
```

```
NEGATIVE: [Full negative prompt from Phase 4]
```

**Settings:**
- Steps: 30-40
- CFG: 5-6 (SDXL)
- Sampler: DPM++ 2M Karras
- ControlNet: IP-Adapter FaceID (weight 0.8), optional OpenPose if specific arm position needed

---

### Scenario B: Professional Brand/Ad Shot

```
PROMPT:
professional commercial photography, shot on Sony A7R IV, 
85mm f/1.4 GM lens, f/2.8 aperture, shallow depth of field,

[YOUR CHARACTER DESCRIPTION],

modeling for a skincare brand advertisement, 
seated at a minimalist white marble table, product bottle 
(blurred) visible on table in foreground, composed neutral 
expression with quiet confidence and direct gaze, jaw relaxed, 
minimal natural makeup (tinted moisturizer, clear lip balm only), 
hair professionally styled but not overly done - natural texture 
with loose face-framing pieces,

wearing a cream silk button-up blouse, fabric texture 
and weave visible, natural draping,

single large softbox positioned camera-right at 45 degrees, 
subtle fill reflection camera-left, creating gentle modeling 
on face with visible shadow transition zones, catchlight in 
upper right of each iris (slightly different shape per eye), 
clean white studio background with very subtle grey gradient,

professional color science, slight contrast, not over-retouched, 
skin texture fully visible, natural color palette
```

---

### Scenario C: Outdoor Lifestyle Candid

```
PROMPT:
candid street photography style, shot on Fujifilm X100V, 
23mm f/2 lens, 1/500 shutter speed, f/4, natural daylight,

[YOUR CHARACTER DESCRIPTION],

walking on a city sidewalk, caught mid-stride, weight on front 
foot, slight motion in hair, looking to her right at something 
off-frame, mouth slightly open mid-word (talking to someone 
out of frame), wearing a slightly oversized denim jacket over 
a black crop top, high-waisted jeans with natural wear marks, 
white sneakers with visible dirt,

harsh afternoon sun from upper-left creating strong directional 
shadows, sunlight catching flyaway hairs, squinting slightly 
against sun, warm highlights on skin with cool blue shadow fill, 
background shows blurred urban environment with other pedestrians,

Fujifilm color science, slightly muted greens and warm skin tones, 
natural film-like quality, slight chromatic aberration at frame edges
```

---

## PHASE 8: POST-GENERATION REFINEMENT PIPELINE

### Step 1: ADetailer Pass
Fix face and hands (covered above)

### Step 2: Hires Fix / Upscale
```
Upscaler: 4x-UltraSharp or NMKD-Siax
Upscale factor: 1.5x - 2x
Denoising: 0.2 - 0.35 (low to preserve details, not regenerate)
```

### Step 3: Inpainting Problem Areas
Common fixes needed:
- Fingers/hands (always check)
- Eye symmetry (if too perfect, BREAK it slightly via inpaint)
- Hair edges (flyaways sometimes get lost)
- Clothing text/logos (often garbled)
- Background anomalies

**Inpainting settings:**
```
Mask blur: 4-8
Denoising: 0.3-0.5
Inpaint area: Only masked
Padding: 32-64 pixels
```

### Step 4: External Post-Processing (Kill remaining AI tells)

**In Photoshop/GIMP/Lightroom:**

1. **Add film grain:** Luminance noise, 3-8%, uniform distribution. Match ISO of your claimed camera.
2. **Lens corrections:** Add subtle barrel distortion or pincushion depending on claimed lens. Add slight chromatic aberration (red/cyan fringing) at high-contrast edges — 0.5-1 pixel.
3. **Imperfect white balance:** Shift slightly warm or cool. Real photos rarely have perfect WB.
4. **Vignetting:** Subtract 5-15% brightness at corners/edges.
5. **Micro-contrast variation:** Real camera sensors produce slightly different contrast response across the frame. Subtle adjustment layers.
6. **EXIF Data:** Strip AI-generation metadata. Add plausible camera EXIF data matching your prompted camera. Tools: ExifTool, Exif Pilot.
7. **Slight JPEG compression:** Re-save at quality 85-92. Real phone photos are compressed. Pristine PNG screams AI.

### Step 5: The "Squint Test" and Detection Tests

Before posting:
1. **Squint test:** Look at the image squinted at arm's length. Does it "feel" like a photo? Trust your gut.
2. **Run through AI detectors yourself:**
   - Hive Moderation (hivemoderation.com)
   - Illuminarty
   - AI or Not (aiornot.com)
   - SynthID detector (if accessible)
3. **Check for frequency domain artifacts:** Tools like Forensically (29a.ch/photo-forensics) can reveal AI patterns in Error Level Analysis (ELA). Real JPEG photos have uniform ELA. AI generations often show inconsistent levels.
4. **Show it to 5 real humans** without context. If anyone says "is this AI?" — go back and fix.

---

## PHASE 9: CONTENT STRATEGY FOR BELIEVABILITY

Technical image quality is only 50% of the battle. The other 50% is **behavioral authenticity**.

### Image Variety Requirements:

Your account needs this distribution to be believable:

| Content Type | Percentage | Quality Level |
|---|---|---|
| Low-effort selfies / mirror pics | 25-30% | Deliberately mediocre: bad angle, poor lighting, grain |
| Casual lifestyle (coffee, walks, food) | 25-30% | Phone quality, natural lighting, slightly off-composition |
| Well-shot content (outfit posts, golden hour) | 20-25% | Good but not studio-perfect |
| Professional/brand content | 10-15% | High quality, deliberate lighting, styled |
| Stories/casual (screenshot, text overlay) | 5-10% | Very low effort, adds to authenticity |

### What Gets People Caught:
- Every single image looks like a magazine editorial → **FAKE**
- Perfect lighting in every image → **FAKE**
- Never any bad angles or unflattering shots → **FAKE**
- Background is always perfectly aesthetic → **FAKE**
- Hands are never clearly visible → **SUSPICIOUS**
- Never shown with other real people → **SUSPICIOUS**
- No progression/variation in hair/skin condition → **SUSPICIOUS**

### Consistency Details to Track:

Create a **character bible** document:

```
NAME: [Character name]
AGE: [Specific age]
SKIN: [Exact skin tone, undertone, specific marks, moles - MAP THEM]
MOLES/MARKS: Left cheek - 2 small moles, neck right side - 1 mole, 
             right forearm - small scar
EYEBROWS: [Exact shape, thickness, arch position]
TEETH: [Any imperfections - slight overlap on lower front teeth, etc.]
BODY TYPE: [Specific, realistic measurements conceptually]
TYPICAL STYLE: [Wardrobe consistency]
HAIR: [Natural texture, typical styles, growth patterns]
ENVIRONMENT: [Where they "live" - consistent apartment/city backgrounds]
```

---

## PHASE 10: COMPLETE COMFYUI WORKFLOW SUMMARY

Here is your node-by-node pipeline:

```
[Load Checkpoint: JuggernautXL v9]
       ↓
[Load LoRA: Raw Photo Style (0.5)]
       ↓
[Load LoRA: Skin Texture Detail (0.4)]
       ↓  
[Load LoRA: Your Character LoRA (0.65)]
       ↓
[CLIP Text Encode - POSITIVE: Full structured prompt]
       ↓
[CLIP Text Encode - NEGATIVE: Full negative prompt]
       ↓
[KSampler]
  - Steps: 35
  - CFG: 5.5
  - Sampler: dpm_2m
  - Scheduler: karras
  - Denoise: 1.0
       ↓
[ControlNet Apply]
  - IP-Adapter FaceID Plus V2 (weight 0.78)
  - Reference: genesis_face.png
       ↓
[ControlNet Apply]  (optional)
  - OpenPose Full (weight 0.65)
  - Reference: pose_reference.png
       ↓
[VAE Decode]
       ↓
[FaceDetailer / ADetailer]
  - Detection model: face_yolov8n
  - Denoise: 0.3
  - Dedicated face prompt + negative
       ↓
[Upscale (Latent)]
  - 4x-UltraSharp
  - Scale: 1.5x
  - Denoise: 0.28
       ↓
[Save Image]
       ↓
[External Post-Processing: Grain, EXIF, compression]
```

---

## QUICK REFERENCE CARD

| Parameter | Candid/Selfie | Lifestyle | Professional |
|---|---|---|---|
| **CFG** | 4.5–5.5 | 5–6 | 6–7 |
| **Steps** | 28–35 | 30–40 | 35–50 |
| **IP-Adapter Weight** | 0.75–0.85 | 0.7–0.8 | 0.7–0.8 |
| **Raw Photo LoRA** | 0.6–0.7 | 0.4–0.5 | 0.2–0.3 |
| **Skin Texture LoRA** | 0.5 | 0.4 | 0.35 |
| **Film Grain (post)** | 6–10% | 4–7% | 2–4% |
| **JPEG Quality (save)** | 82–88 | 85–92 | 92–96 |
| **Resolution** | 1024x1365 (3:4) | 1024x1024 / 1024x1536 | 1024x1536 (2:3) |
| **Camera to prompt** | iPhone/Samsung | Fujifilm/Ricoh | Sony/Canon |
| **Lighting** | Bad/mixed/flat | Natural/golden hour | Controlled/softbox |

---

This pipeline, executed consistently with discipline and attention to micro-details, will produce output that passes casual inspection, algorithmic detection, and critical human review. The key is **imperfection by design** — every deliberate flaw you engineer is a shield against detection.
