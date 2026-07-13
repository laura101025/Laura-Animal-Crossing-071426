Technical Specification: Animal Crossing Style RPG Sandbox
Full-Stack Architecture, Rendering Mechanics, and Generative AI Integration
1. Executive Summary
This document details the architectural design and technical specification for Remix: Animal Crossing RPG Sandbox, a full-stack, client-rendered sandbox experience that captures the aesthetic whimsy and structural simulation of classic cozy life simulators.
The application is built on a unified React 19 + Vite 6 frontend paired with an Express 4 server, written entirely in type-safe TypeScript. By combining a custom HTML5 Canvas rendering pipeline with server-side AI engines driven by the @google/genai SDK, the platform delivers a deep, interactive town-building experience.
code
Code
+---------------------------------------------------------------------------------+
|                                  VITE PREVIEW                                   |
|                                                                                 |
|   +----------------------- HTML5 CANVAS VIEWPORT ---------------------------+   |
|   |  - Rolling Log / Isometric Projection                                   |   |
|   |  - Live Particle Systems (Petals, Leaves, Rain)                         |   |
|   |  - Dynamic Lighting (Morning, Afternoon, Evening, Night)                |   |
|   +-------------------------------------------------------------------------+   |
|                                                                                 |
|   +------------------------- COMPONENT OVERLAYS ----------------------------+   |
|   |  - Villager Chat (Bob, Rosie, Raymond, Marshal, Fauna)                  |   |
|   |  - Nook's AI DIY Recipe Builder (Dynamic Crafting)                      |   |
|   |  - Town Hall Bulletin Board (Procedural Events)                         |   |
|   +-------------------------------------------------------------------------+   |
+----------------------------------------+----------------------------------------+
                                         | HTTP / REST (JSON)
                                         v
+---------------------------------------------------------------------------------+
|                                EXPRESS BACKEND                                  |
|                                                                                 |
|   +-- ROUTING INTERFACE ----------------------------------------------------+   |
|   |  - POST /api/villager/chat        -> Custom LLM Personality Agents      |   |
|   |  - POST /api/sculptor/diy         -> Interactive SVG & Stat Generation  |   |
|   |  - POST /api/townhall/bulletin    -> Daily Event Chronicle & Gossip     |   |
|   |  - POST /api/kk/ballad            -> Lyria Clip Procedural Audio        |   |
|   +-------------------------------------------------------------------------+   |
|                                        |
|                                        | @google/genai SDK
|                                        v
|   +-- GEMINI CORE COGNITION ENGINE -----------------------------------------+   |
|   |  - Model: gemini-3.5-flash (Text & Structured Reasoning)                |   |
|   |  - Model: lyria-3-clip-preview (Procedural Audio Ballads)                |   |
|   +-------------------------------------------------------------------------+   |
+---------------------------------------------------------------------------------+
Primary Technical Goals
Interactive Simulation: Maintain a smooth 60 FPS rendering pipeline in the browser, capable of project-shifting coordinate translations, procedural particle rendering, and dynamic lighting sweeps.
Deterministic Mechanics: Implement a strict grid-based map state that governs collisions, interaction zones, coordinate bounds, and environmental persistence.
Natural Language Cogency: Expose deep conversational agents modeled after Animal Crossing villagers, maintaining high context-awareness and cohesive personalities without client-side API key exposure.
Procedural Crafting: Drive an open-ended DIY system that parses arbitrary natural language inputs into valid, structured, and visually renderable sandbox elements.
2. Visual Theme & Spatial Styling
The aesthetic direction of the sandbox is defined as the "Naturalist Cozy" Theme.
Color Palette Spec
The visual layers avoid the aggressive saturation of standard digital grids, employing muted earthy tones, organic warm pastels, and soft botanical greens.
Natural Base Screen Background: #F9F6E5 (Warm Cream / Soft Parchment). Reduces visual fatigue and mimics clean, sun-bleached wood or woven straw mats.
Primary Text & Details: #5D4037 (Rich Earthwood / Chestnut). Completely replaces harsh #000000 text to maintain a softer, hand-carved look.
Header Boundaries: #8BC34A (Fresh Clover Green). Highlighted with a #7CB342 border to establish structure and mimic lush hedges.
Border Accents: #D7CCC8 (Soft Linen / Distressed Clay). Frames panels with subtle depth.
Accent Lights: #81C784 (Dewy Moss Green) and #FFB74D (Sunlit Amber). Used for active states, selected tools, and interactable nodes.
Typographic Hierarchy
The interface implements a dual-font framework designed to balance legibility and technical styling:
Display Sans-Serif (Inter): Governs primary reading panes, interactive dialogues, and town bulletins. Styled with tight tracking (tracking-tight) and balanced weight scales to maintain readability inside floating iFrames.
Display Monospace (JetBrains Mono / Fira Code): Applied to systemic readouts, coordinate systems, and environmental metrics. It emphasizes a hand-crafted, deliberate retro-RPG layout.
Layout Density and Negative Space
Unlike crowded dashboards, the layout embraces generous, breathable negative space. Padding scales are non-uniform to create rhythm: interfaces feature thick outer boundaries (e.g., p-6 to p-8) with micro-padded lists (e.g., p-2) to guide focus directly to the canvas viewport. Cards are finished with thick, rounded borders (rounded-2xl or rounded-3xl) and deep, soft shadows to replicate the appearance of modular wooden blocks resting on a plush surface.
3. Full-Stack System Architecture
The application is structured as a single-entry full-stack system designed to run inside sandboxed cloud containers, routing traffic exclusively through Port 3000 to the public proxy.
code
Code
/ (Workspace Root)
├── .env.example                # Declares required keys (GEMINI_API_KEY, APP_URL)
├── .gitignore                  # Prevents committing dependencies and build logs
├── index.html                  # Core HTML5 entry point hosting root division
├── metadata.json               # Defines name, description, and server capabilities
├── package.json                # Bundler configurations and project scripts
├── server.ts                   # Express server with Vite middleware integrations
├── tsconfig.json               # Strict compiler rules (Target: ES2022)
├── vite.config.ts              # Custom alias mapping, asset routing, and HMR control
├── src/
│   ├── main.tsx                # StrictMode client renderer
│   ├── App.tsx                 # Core orchestrator, state manager, and Canvas loop
│   ├── types.ts                # Rigid data structures, enums, and state schemas
│   ├── components/
│   │   ├── VillagerChatCard.tsx    # Conversational UI with streaming inputs
│   │   ├── DIYRecipeBuilder.tsx    # Nook's AI Sculpting parser interface
│   │   └── TownHallBulletin.tsx    # Procedural town chronicle and events pane
│   └── utils/
│       └── audio.ts            # Acoustic tone generators & synthesizer context
Build & Server Run Scripts
To operate successfully in production and development container hosts, package.json configures a unified transpile-and-bundle process:
code
JSON
{
  "type": "module",
  "scripts": {
    "dev": "tsx server.ts",
    "build": "vite build && esbuild server.ts --bundle --platform=node --format=cjs --packages=external --sourcemap --outfile=dist/server.cjs",
    "start": "node dist/server.cjs"
  }
}
Development Phase: Running npm run dev boots the backend using tsx. The server intercepts the entry point, initializes its API endpoints, and instantiates the Vite development server in middleware mode to serve hot client assets.
Production Build Phase: Running npm run build triggers a two-part compilation. First, the Vite client builds, generating optimized static bundles inside /dist. Second, esbuild bundles server.ts into a single, self-contained CommonJS file (dist/server.cjs). All relative imports are resolved at build-time, bypassing Node's strict runtime ES Module directory checks, while keeping external library packages clean via --packages=external.
Execution Phase: The deployment host executes npm run start, spinning up dist/server.cjs in production mode. Static assets are served via express.static('dist') with wildcard routing fallback to index.html.
4. Core Graphics & Camera Projection Engines
The central viewport consists of a high-performance HTML5 Canvas API rendering pipeline managed by a React lifecycle. It operates at a target 60 FPS via a requestAnimationFrame loop, translating a 2D tile-grid coordinate space into three-dimensional projections in real-time.
code
Code
[World Coordinates: (wx, wy)]
                     |
                     v
   +-----------------------------------+
   |  Isometric Projection (Optional)  |
   |  ix = (wx - wy) * cos(30)         |
   |  iy = (wx + wy) * sin(30)         |
   +-----------------------------------+
                     |
                     v
   +-----------------------------------+
   |  Rolling Log Distortion (Curved)  |
   |  dy = wy - Player_Y               |
   |  Distortion = (dy^2) / (2 * R)    |
   |  ry = wy - Distortion             |
   +-----------------------------------+
                     |
                     v
   +-----------------------------------+
   |     Dynamic Lighting Overlay      |
   |  Morning: Soft Gold Amber         |
   |  Afternoon: Vivid Clear Warmth    |
   |  Evening: Dusty Lavender Amber    |
   |  Night: Deep Indigo Blue Hue      |
   +-----------------------------------+
                     |
                     v
          [Render to Screen Canvas]
Camera Projection Formulas
The system supports 5 distinct camera views to mimic retro-RPG perspective shifts:
This mode projectively warps tiles to simulate a cylindrical world rolling forward. The mathematical foundation maps flat vertical coordinates onto a cylinder of radius 
:
Given a tile's world vertical coordinate 
 and the player's focus position 
:
Calculate the relative vertical offset:
Compute the rolling log vertical compression and drop:
The final rendered screen coordinate 
 is:
For an optimal layout, the cylinder radius 
 is scaled to 
. Tiles far from the vertical center slope downward, compressing toward the horizon. This simulates the curved-world horizon signature of Animal Crossing: Wild World and City Folk.
A traditional 
 projection. The standard layout translates a tile's grid indices 
 with a 
 pixel ratio:

A clean, orthographic retro grid layout. Grid coordinates map directly to screen offsets:

Centers the camera on the player, tracking movement with a soft linear interpolation (LERP) delay:

An ultra-close macro view. The render scale is multiplied by 
, centering tightly on the player tile while introducing an atmospheric radial vignette overlay to obscure outer tile structures.
Dynamic Lighting & Time-of-Day Spec
An offscreen lighting layer is alpha-blended over the canvas tiles using the globalCompositeOperation = 'multiply' configuration.
code
Code
[Time of Day]   -->   [Color Gradient Blend]             -->   [Composite Overlay]
Morning               Gold-Orange (#FFB300, Alpha 0.15)         Soft Glow Warmth
Afternoon             No Overlay (Clear Viewport)               Crisp & Vibrant
Evening               Dusty Violet/Crimson (Alpha 0.3)          Cozy Golden Dusk
Night                 Deep Indigo Blue (#1A237E, Alpha 0.55)    Moody Moonlight
Real-time Particle Engine
To elevate visual charm, an active particle array is updated and rendered on each frame tick:
Petal Drift (Cherry Blossom Season): Floating pink ellipses (#F48FB1) moving on a slow sine-wave drift vector:

Rain Particles: Fast linear slashes of soft blue (rgba(179,229,252,0.4)) rendered from high-left to low-right:

Fallen Leaf Swirls: Warm orange-brown particles spinning on an angular rotation velocity scale.
5. Data Engine & Game State Representation
State modeling is centralized inside /src/types.ts to enforce deterministic behavior across client modules.
Game State Models
code
TypeScript
export type ViewMode = 'rolling' | 'isometric' | 'topdown' | 'dramatic' | 'zoom';
export type ToolType = 'watering' | 'shovel' | 'net' | 'fishing' | 'paint' | 'inspect';

export interface Character {
  id: string;
  name: string;
  avatar: string;
  description: string;
}

export interface Villager {
  id: string;
  name: string;
  species: string;
  personality: 'Lazy' | 'Peppy' | 'Snooty' | 'Normal' | 'Jock' | 'Cranky';
  avatar: string;
  catchphrase: string;
  greeting: string;
  relationship: string;
}

export interface MapElement {
  id: string;
  name: string;
  type: 'flower' | 'tree' | 'animal' | 'fossil' | 'gift' | 'custom';
  subType?: 'butterfly' | 'fish' | 'bug' | 'foliage';
  color: string;
  symbol: string;
  description?: string;
  wateredCount?: number;
  scale?: number;
}

export interface MapTile {
  terrain: 'grass' | 'sand' | 'river' | 'path';
  element: MapElement | null;
  wateredToday?: boolean;
}

export interface TownMap {
  name: string;
  grid: MapTile[][];
}

export interface MuseumCollection {
  bugs: string[];
  fish: string[];
  fossils: string[];
  diyItems: string[];
  bells: number;
}
Storage & Sync Strategy
The sandbox implements a hybrid synchronization pipeline.
Transient Action-State: Character selection, active tools, viewport transformations, and visual particle configurations are managed purely within React's dynamic hook tree to maximize render loop performance.
Persistent Town-State: Map layouts, custom DIY structures, current relationship scores, and the museum vault are backed up into localStorage under the key cozy_sandbox_state. On system boot, a structural parser verifies if stored JSON aligns with the schema definitions. If any field is broken or missing, it initializes a fresh town layout using the procedural grid generator.
6. Unified Server-Side Gemini AI Integration
All AI-driven features are routed through the backend using @google/genai to ensure the GEMINI_API_KEY remains secure.
code
Code
[React Client]                           [Express Server]                      [Gemini API]
      |                                         |                                    |
      |   POST /api/villager/chat               |                                    |
      |   (Payload: Villager identity, history) |                                    |
      | --------------------------------------> |                                    |
      |                                         |  Initialize SDK with               |
      |                                         |  headers: 'User-Agent':            |
      |                                         |  'aistudio-build'                  |
      |                                         |                                    |
      |                                         |  ai.models.generateContent({       |
      |                                         |    model: 'gemini-3.5-flash',      |
      |                                         |    contents: formattedPrompt,      |
      |                                         |    config: { ... }                 |
      |                                         |  })                                |
      |                                         | ---------------------------------> |
      |                                         |                                    |
      |                                         | <--------------------------------- |
      |                                         |  Returns response text             |
      |                                         |                                    |
      | <-------------------------------------- |                                    |
      |   JSON: { reply: "catchphrase!" }       |                                    |
Shared Server-Side Gemini client
The client is declared at the module level in server.ts using the correct named parameter instantiation:
code
TypeScript
import { GoogleGenAI } from "@google/genai";

const ai = new GoogleGenAI({
  apiKey: process.env.GEMINI_API_KEY,
  httpOptions: {
    headers: {
      'User-Agent': 'aistudio-build',
    }
  }
});
1. Dynamic Villager Chat Endpoint (POST /api/villager/chat)
This endpoint handles dynamic conversation with our interactive villagers. The server maps the incoming request payload into a system instruction that locks the model into the villager's persona.
Selected Model: gemini-3.5-flash
Rationale: This model provides low-latency, highly coherent text generation, perfect for real-time conversational agents.
code
JSON
{
  "villagerId": "bob",
  "message": "Hey Bob! Want to go bug catching later?",
  "history": [
    { "role": "user", "text": "Hello Bob!" },
    { "role": "model", "text": "Oh, hey there! I was just taking a nap on this cozy moss, hoofin!" }
  ]
}
code
TypeScript
app.post("/api/villager/chat", async (req, res) => {
  const { villagerId, message, history } = req.body;
  const villager = INITIAL_VILLAGERS.find(v => v.id === villagerId);
  
  if (!villager) {
    return res.status(404).json({ error: "Villager not found" });
  }

  const systemInstruction = `
    You are ${villager.name} from Animal Crossing. You are a ${villager.personality} ${villager.species}.
    Your signature catchphrase is "${villager.catchphrase}".
    You must always speak in character, matching a cozy, lighthearted, and slightly whimsical tone.
    Ensure you naturally sprinkle your catchphrase "${villager.catchphrase}" into your sentences. Keep your responses short (under 3 sentences) and warm.
  `;

  try {
    const response = await ai.models.generateContent({
      model: "gemini-3.5-flash",
      contents: message,
      config: {
        systemInstruction,
        temperature: 0.8,
        maxOutputTokens: 150
      }
    });

    res.json({ reply: response.text });
  } catch (error) {
    res.status(500).json({ error: "Cognition loop interrupted" });
  }
});
2. Nook's AI Sculpting Endpoint (POST /api/sculptor/diy)
Generates structured custom items from arbitrary text prompts using a strict JSON schema.
Selected Model: gemini-3.5-flash
Rationale: Ideal for structured data extraction and schema-conforming JSON generation.
code
JSON
{
  "prompt": "An elegant clock made of glowing moonstones"
}
code
JSON
{
  "name": "Moonstone Clock",
  "symbol": "🔮",
  "color": "#e0f2fe",
  "description": "A beautifully carved timepiece that hums with soft lunar light.",
  "requiredBells": 1200,
  "theme": "Celestial"
}
3. Town Hall Bulletin Engine (POST /api/townhall/bulletin)
Combines current environmental variables (weather, time, dynamic player actions) to compile a procedurally generated daily local news chronicle.
Selected Model: gemini-3.5-flash
Rationale: Generates stylized, context-rich narratives from structured simulation states.
code
JSON
{
  "townName": "Acorn Valley",
  "weather": "cherry",
  "timeOfDay": "afternoon",
  "recentLog": "Fished a Coelacanth; watered 3 roses; built a Moonstone Clock."
}
7. Three "Wow" AI Features Specified
To elevate this sandbox from a static canvas painting tool to a highly immersive, responsive simulation, we introduce three deep, interconnected AI features.
code
Code
[ TOWN STATE ]
                  - Weather, Time, Player Actions, Inventory, Grid
                                         |
         +-------------------------------+-------------------------------+
         |                               |                               |
         v                               v                               v
+------------------+           +------------------+            +------------------+
|  K.K. SLIDER'S   |           |    TOM NOOK'S    |            |   DREAM DIARY    |
|   LYRIA BALLAD   |           |  ZONING PLANNER  |            |  DRIFT GENERATOR |
|  - Lyria Music   |           |  - Layout analysis|           |  - Relationship  |
|  - Custom Lyrics |           |  - Bell appraisal|            |    drifts        |
|  - Visual Album  |           |  - Architecture  |            |  - Nightly logs  |
+------------------+           +------------------+            +------------------+
AI Feature 1: K.K. Slider's AI Lyria Ballad Generator (Dynamic Procedural Acoustic Ballad Synthesizer)
In classic games, K.K. Slider plays signature static audio loops. In this sandbox, K.K. Slider operates a server-side Lyria Music Engine. The player inputs their current mood or an arbitrary topic (e.g., "strolling through the cherry blossoms" or "feeling lazy on a rainy night"), and the backend generates a unique acoustic ballad with custom synthesized lyrics, complete with playable base64 audio.
code
Code
[Player Input Mood / Theme]
            |
            v
+-------------------------------------------------------------+
|               POST /api/kk/ballad (Backend)                 |
|                                                             |
| 1. Request Lyria-3-clip-preview                             |
|    Prompt: "A dynamic, warm, acoustic folk guitar melody      |
|    inspired by: [Player Mood]. Include sweet, cozy lyrics."  |
| 2. Extract Audio base64 chunk array                         |
| 3. Extract Lyrics & Title                                   |
+-------------------------------------------------------------+
            |
            v
[Returned Audio Blob URL & Kinetic Scrolling Lyric Card]
API Route (POST /api/kk/ballad):
Model: lyria-3-clip-preview (Lyria Clip for up to 30-second songs).
Processing: The endpoint formats the prompt, request parameters, and streams back the generated audio data.
Audio Reconstruction on Frontend:
The client accumulates base64 chunks, decodes them into a binary array, and constructs a playable object URL:
code
TypeScript
const binary = atob(audioBase64);
const bytes = new Uint8Array(binary.length);
for (let i = 0; i < binary.length; i++) {
  bytes[i] = binary.charCodeAt(i);
}
const blob = new Blob([bytes], { type: "audio/wav" });
const balladUrl = URL.createObjectURL(blob);
Kinetic Lyrics UI Component:
The lyrics returned alongside the audio stream are displayed on a parchment-style lyric card. Utilizing motion/react, the words scroll kinetically in sync with the song's playback progress.
AI Feature 2: Tom Nook's AI Landscape and Architecture Appraisal and Zoning Planner
Instead of placing items randomly on a grid, players can submit their entire grid layout to Tom Nook's Appraiser. Nook analyzes the placement of pathways, river alignments, foliage density, and building layout, returning a comprehensive appraisal score (S, A, B, C, or D rank). He also offers actionable advice on landscaping and zoning improvements.
code
Code
[Current Grid State Matrix]
            |
            v
+-------------------------------------------------------------+
|               POST /api/nook/appraise (Backend)             |
|                                                             |
| 1. Model: gemini-3.5-flash                                  |
| 2. Input JSON Schema: Grid, Terrain tiles, Elements          |
| 3. Output Schema:                                           |
|    - Score Rank (S to D)                                    |
|    - Dynamic land appraisal value in Bells                  |
|    - Specific coordinate suggestions for aesthetic balance  |
|    - Humorous business commentary from Tom Nook             |
+-------------------------------------------------------------+
            |
            v
[Appraisal Overlay with Highlighted Canvas Target Coordinates]
State Mapping: The 2D grid of elements (such as flower-cosmos at row 4, col 2) is compressed into a coordinate-dense JSON representation and sent to the server.
Appraisal Core Prompt:
The system prompt establishes Tom Nook's personality: a business-oriented, Bell-loving raccoon with a soft spot for clean layouts. It enforces a strict structured JSON schema:
code
TypeScript
const nookAppraisalSchema = {
  type: Type.OBJECT,
  properties: {
    rank: { type: Type.STRING, description: "Rank from S to D" },
    bellValue: { type: Type.INTEGER, description: "Estimated market value in Bells" },
    critique: { type: Type.STRING, description: "Tom Nook's conversational analysis" },
    recommendations: {
      type: Type.ARRAY,
      items: {
        type: Type.OBJECT,
        properties: {
          coordinate: { type: Type.STRING, description: "e.g., 'row-2-col-4'" },
          suggestion: { type: Type.STRING, description: "What to build or clear here" }
        }
      }
    }
  },
  required: ["rank", "bellValue", "critique", "recommendations"]
};
UI Canvas Highlights:
When the player views Nook's zoning advice, the suggestions are highlighted directly on the canvas as pulsating green grids using standard context draw methods:
code
TypeScript
// Highlight coordinate e.g. Row 2, Col 4
ctx.strokeStyle = "rgba(129, 199, 132, 0.85)";
ctx.lineWidth = 3;
ctx.setLineDash([6, 4]);
ctx.strokeRect(col * TILE_WIDTH, row * TILE_HEIGHT, TILE_WIDTH, TILE_HEIGHT);
AI Feature 3: Villager Personality & Relationship Drift and Dream Diary Generator
In classic sandbox simulators, relationship values are static, increments are predictable, and villager states do not update unless clicked. This feature introduces a Nightly Dream Drift Engine that processes daily player actions, environmental changes, and conversational history to generate procedural "Dreams" for each villager. This results in personality drifts, catchphrase changes, and updated relationship scores.
code
Code
[Conversational Log & Watering/Digging History]
            |
            v
+-------------------------------------------------------------+
|               POST /api/simulation/drift (Backend)          |
|                                                             |
| 1. Model: gemini-3.5-flash                                  |
| 2. Processes:                                               |
|    - "Did player ignore me today?"                          |
|    - "Did player water flowers near my house?"              |
|    - "Did we chat about music or bugs?"                     |
| 3. Output Schema:                                           |
|    - New Relationship Tier (e.g. "Besties", "Distant")      |
|    - Dream Diary Log: An expressive, cozy diary entry from  |
|      the villager's perspective detailing their dreams.      |
|    - Adaptive Catchphrase Drift (proposes new phrase)       |
+-------------------------------------------------------------+
            |
            v
[Morning Bulletin / Post Office Letter containing the Dream Diary Log]
Action Buffer: Every tool interaction, item placement, and conversation is logged inside a lightweight, client-side session log array:
const sessionLog = ["Watered pink tulip near Bob", "Talked to Rosie about pop stars", "Ignored Marshal"];
Nightly Simulation Run: On a "day pass" transition, the transaction history is transmitted to the server. The gemini-3.5-flash model evaluates the log and adjusts the relationship tier accordingly.
The Dream Diary: The system returns a cozy, whimsical dairy entry explaining why the villager's mood changed. For example, if the player ignored Marshal but watered flowers near his house, Marshal writes: "I had a strange dream under the cherry trees... a mysterious giant was rain-showering my precious roses while avoiding me entirely. Maybe I should stop being so sulky, sulky..." This entry is delivered to the player's post office box, which is accessible via the town bulletin.
8. Bug Analysis & Structural Fixes
Reviewing the initial files reveals four critical architectural vulnerabilities that must be resolved to guarantee stability.
1. Canvas Sizing and Container Resizing Vulnerability
The initial canvas implementation references standard fixed client properties or hardcoded offsets. Inside an nested iFrame wrapper, standard event listeners such as window.addEventListener('resize') fail to capture changes in container bounds, leading to skewed aspect ratios, pixelation, or clipping.
Inject a highly precise ResizeObserver callback onto the wrapper element parent, updating internal state dimensions dynamically, and managing clean observer disposal.
code
TypeScript
// Fix implementation snippet for canvas container state
const containerRef = useRef<HTMLDivElement | null>(null);
const [dimensions, setDimensions] = useState({ width: 800, height: 480 });

useEffect(() => {
  if (!containerRef.current) return;

  const observer = new ResizeObserver((entries) => {
    for (const entry of entries) {
      const { width, height } = entry.contentRect;
      // Debounce and update dimensions
      setDimensions({
        width: Math.max(width, 400),
        height: Math.max(height, 300)
      });
    }
  });

  observer.observe(containerRef.current);
  return () => observer.disconnect();
}, []);
2. React state mutations inside the requestAnimationFrame loop
Updating player coordinates (
) or particle properties inside standard React states at 60 FPS triggers frequent component re-renders. This can cause the canvas element to re-instantiate, degrading performance on lower-end devices.
Store position coordinates and particle arrays in React useRef hooks to bypass the state scheduler entirely. Render the canvas by writing values to the canvas context directly from the mutable references, and sync back to React state only when the player stops moving or performs an action (e.g., watering a plant).
code
TypeScript
// Ref-driven movement state integration
const playerPos = useRef({ x: 400, y: 240 });
const particles = useRef<any[]>([]);

const tick = () => {
  // Update coordinates via mutable refs directly
  if (keysPressed.current['w']) playerPos.current.y -= 3;
  if (keysPressed.current['s']) playerPos.current.y += 3;
  
  const ctx = canvasRef.current?.getContext('2d');
  if (ctx) {
    drawMap(ctx, playerPos.current.x, playerPos.current.y);
    drawParticles(ctx, particles.current);
  }
  
  requestAnimationFrame(tick);
};
3. Overlapping Web Audio Synthesizer Buffers
In the initial src/utils/audio.ts utility file, tone generators trigger simple audio oscillators on every tool usage without clean frequency sweeps or active oscillator teardown. If a player rapidly presses keys to water multiple plants, the oscillators pile up, causing audio clipping and eventual system lag.
Implement a strict monophonic audio channel scheduler that tracks the active tone generator and cleanses active audio nodes using custom exponential ramps before initiating a new pitch.
code
TypeScript
class SoundScheduler {
  private ctx: AudioContext | null = null;
  private activeOscillator: OscillatorNode | null = null;

  public playTone(freq: number, duration: number) {
    if (!this.ctx) {
      this.ctx = new (window.AudioContext || (window as any).webkitAudioContext)();
    }
    
    // Smoothly kill previous oscillator to avoid popping and memory exhaustion
    if (this.activeOscillator) {
      try {
        this.activeOscillator.stop();
        this.activeOscillator.disconnect();
      } catch (e) {}
    }

    const osc = this.ctx.createOscillator();
    const gainNode = this.ctx.createGain();

    osc.connect(gainNode);
    gainNode.connect(this.ctx.destination);

    osc.frequency.setValueAtTime(freq, this.ctx.currentTime);
    
    // Add volume envelope: attack-decay-release (ADR)
    gainNode.gain.setValueAtTime(0, this.ctx.currentTime);
    gainNode.gain.linearRampToValueAtTime(0.2, this.ctx.currentTime + 0.05);
    gainNode.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);

    osc.start();
    osc.stop(this.ctx.currentTime + duration);
    this.activeOscillator = osc;
  }
}
4. Environment-Specific HMR Websocket Handshake Failure
Since Vite's standard Hot Module Replacement relies on a direct WebSocket connection on port 5173 or port 3000, running in a nested browser iFrame with HMR enabled triggers constant console connection loops, resulting in heavy CPU load.
The dev server must run with HMR disabled (DISABLE_HMR=true) inside the sandbox environment. This is already handled gracefully in our vite.config.ts, ensuring files are compiled statically without continuous websocket reconnect attempts. This avoids flickering and saves CPU during incremental code updates.
9. Prompt Engineering Specifications
These production-ready system prompts ensure that our Gemini endpoints return responses in the correct format.
Prompt Specification for POST /api/sculptor/diy (Nook's AI Sculpting Engine)
code
Code
SYSTEM INSTRUCTION:
You are Tom Nook, president of Nook Inc. and developer of Acorn Valley. You are an expert landscape designer, builder, and developer.
Your job is to parse arbitrary user inputs into a structured DIY sandbox recipe.

You MUST respond strictly in valid, parseable JSON conforming to this schema. Do not output markdown code blocks. Do not prefix or append any conversational text.

{
  "name": "string (the clean, beautifully presented item name)",
  "symbol": "string (exactly one emoji that best represents this object in a retro 2D style)",
  "color": "string (hex code of the item's main theme color, optimized for dark or cream canvases)",
  "description": "string (a whimsical, charming, cozy description in Tom Nook's voice)",
  "requiredBells": "number (an integer between 200 and 5000, representing the cost of materials)",
  "theme": "string (e.g., 'Celestial', 'Forest', 'Cozy', 'Nautical', 'Industrial')"
}

CRITICAL RULES:
1. Ensure the color has excellent contrast against cream (#F9F6E5) and grass (#C5E1A5) backgrounds.
2. The description must sound exactly like Tom Nook: professional, business-minded, but incredibly warm and polite. Include his signature chuckle "yes, yes!" or "hm?" occasionally.
3. If the user input is nonsense, generate a charming "Mystery Trash Item" with low Bell value.
Prompt Specification for POST /api/townhall/bulletin (Town Hall News Chronicle)
code
Code
SYSTEM INSTRUCTION:
You are the Town Hall Bulletin Board, compiling Acorn Valley's daily local chronicle.
You will receive a state object detailing current weather conditions, time of day, and recent player actions.
Your task is to write a highly stylized, heartwarming, and cozy town bulletin.

The bulletin must contain:
1. A seasonal greeting based on the current weather.
2. A whimsical news report highlighting the player's recent activities (e.g., catching a fish, watering plants, crafting items).
3. A humorous piece of town gossip involving one of our villagers (Bob, Rosie, Raymond, Marshal, or Fauna).
4. An encouraging sign-off.

CRITICAL RULES:
1. Speak in a comforting, pastoral voice, like a handwritten note pinned to an oak tree.
2. Keep the response compact: exactly 4 short paragraphs.
3. Integrate the input state parameters naturally. Do not print raw variable names or technical code tags.
4. Output cleanly in formatted Markdown with line breaks. No raw HTML.
Prompt Specification for POST /api/kk/ballad (K.K. Slider's Lyria Ballad Engine)
code
Code
SYSTEM INSTRUCTION:
You are K.K. Slider, the legendary dog musician. You play soulful acoustic tunes for free-spirited travelers.
Your job is to generate a beautiful, raw, acoustic folk guitar song based on the user's input mood.

You MUST respond strictly in valid JSON format. Do not write introductory or concluding text outside the JSON block.

{
  "title": "string (a poetic, acoustic-sounding song title)",
  "lyrics": "string (four stanzas of cozy, folk lyrics with chords in brackets, e.g., '[C] Walking down the pathway... [G]')",
  "tempo": "number (beats per minute, between 60 and 110)",
  "mood": "string (e.g., 'Melancholic', 'Whimsical', 'Sunny Day', 'Breezy Night')"
}

CRITICAL RULES:
1. The lyrics must feel organic, poetic, and relaxed.
2. Sprinkled between chords, include classic K.K. acoustic vocables: "How-do-woh", "Que-que", "Me-mi-ma".
3. Maintain K.K. Slider's relaxed, cool-dog tone: "Heh... keep your feet traveling, man."
10. Verification & Quality Assurance Protocols
To guarantee seamless operation of the full-stack system inside sandboxed environments, a strict validation suite must be run before deploying code changes.
code
Code
[ RUN LINTER: npm run lint ]
                     |
         +-----------+-----------+
         |                       |
      [FAIL]                  [PASS]
         |                       |
  Analyze output,                v
  fix syntax/types     [ RUN BUILD: npm run build ]
         |                       |
         |           +-----------+-----------+
         |           |                       |
         |        [FAIL]                  [PASS]
         |           |                       |
         +---------> |                       v
                Analyze bundler       [ RESTART DEVELOPMENT SERVER ]
                errors & resolve             |
                                             v
                                      [ MANUAL COZY SMOKE TEST ]
                                      - Verify Canvas 60 FPS loop
                                      - Test Villager chat APIs
                                      - Check dynamic views
Step 1: Strict Syntactic Check via lint_applet
Every incremental update is first validated using the workspace linter:
Target: Ensure all imported structures are fully resolved. Ensure import statements are placed exclusively at the top of files and use named imports rather than object destructuring.
Enum Verification: Verify that all structures in /src/types.ts declare standard, non-const enum definitions to prevent transpiler issues.
Step 2: Sandbox Asset Compilation via compile_applet
After passing lint checks, the production compiler compiles client assets and the server backend:
Transpile Check: Ensure TypeScript successfully strips types without throwing compile errors.
Bundling Check: Confirm esbuild correctly generates the self-contained dist/server.cjs file, verifying that all external dependencies are cleanly resolved.
Step 3: Server Reboot via restart_dev_server
When updating server.ts or modifying package.json scripts:
Trigger restart_dev_server to shut down stale Node.js parent processes, spin up the new Express engine, and re-inject Vite's middleware pipeline.
Step 4: The 4-Step Cozy Smoke Test
Before marking a development sprint as complete, execute the following manual tests in the viewport:
Stroll Fluidity: Walk the player across different terrain types (grass, path, sand, river). Ensure movement is smooth and particle emitters (e.g., cherry blossoms) update correctly.
View Matrix Shift: Cycle through all 5 camera view modes. Confirm that coordinates transform correctly and that the "rolling log" cylinder drops tiles seamlessly without screen tearing.
Generative AI Chat Call: Trigger a chat message with Bob. Ensure the server intercepts the payload, communicates with the Gemini model on the backend, and displays Bob's customized catchphrase-laden reply correctly.
DIY Sculpting & Inventory Placement: Prompt Tom Nook to build a custom item. Confirm that the system parses the JSON correctly, adds the item to the collection, and allows the player to place the custom symbol on the grid.
11. Architectural & UX Follow-up Questions
To help refine this full-stack sandbox experience, consider the following 20 follow-up questions:
Simulation & Mechanics
How should we handle grid collision checks when the player transitions between different camera views (e.g., Isometric vs. the curved Rolling Log view)?
Should river tiles block player movement entirely unless they have a "vaulting pole" tool equipped?
How should we model wind direction vectors so that floating balloon presents drift realistically across the grid?
Should watered flowers require multiple consecutive daily sessions to cross-breed into rare hybrid colors (e.g., red + yellow cosmos = orange cosmos)?
What formula should govern the daily spawning of fossil cracks—should they spawn on random empty grass tiles, or cluster near trees?
AI & Natural Language
How should we handle chat history in the POST /api/villager/chat payload to prevent token bloat during long conversational sessions?
Should villagers remember the player's past gifts, referencing them dynamically in conversations (e.g., "Thanks for that Moonstone Clock you crafted!")?
If the player sets a custom town motto on the Town Hall bulletin board, how should we inject it into the villagers' system prompts?
Can we implement real-time speech translation (gemini-3.5-live-translate-preview) so players can speak into their microphone and hear villagers reply in a stylized language like "Animalese"?
How should Tom Nook's Appraiser evaluate dynamic terrain changes, such as the ratio of paved stone paths to natural green grass?
Performance & Graphics
How should the HTML5 Canvas pipeline optimize rendering when drawing large grids, and should we implement frustum culling to skip drawing offscreen tiles?
Should we implement an offscreen canvas to pre-render static terrain tiles, reducing redraw operations on each frame tick?
How should the particle engine scale on lower-end devices—should it automatically reduce the particle count if the frame rate drops below 45 FPS?
What approach should we take to handle asset pre-loading (e.g., custom SVG structures or sound effects) to prevent visual popping on boot?
How should we configure the Web Audio API context to prevent browser security blocks from muting background audio before the player interacts with the page?
Experience, Economy & Progression
How should the dynamic appraisal value of land in Tom Nook's Zoning Planner affect the daily interest rates of player loans?
Should we introduce a "turnip marketplace" on the Town Hall bulletin board, where prices are driven by procedural forecasts?
How should the museum collection handle duplicate fossils or insect species—should they be sellable for Bells, or can they be gifted to other villagers?
Should K.K. Slider's acoustic Lyria Ballads be collectible, allowing players to save them to a custom vinyl player element in their homes?
What system should govern villager moving cycles—should a villager announce their plan to leave on the bulletin board, giving the player a chance to convince them to stay?
