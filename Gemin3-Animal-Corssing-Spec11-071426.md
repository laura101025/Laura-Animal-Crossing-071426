TECHNICAL SPECIFICATION: ANIMAL CROSSING: CITY FOLK RPG SANDBOX APPLET
1. Executive Summary
The Animal Crossing: City Folk RPG Sandbox is a modern, web-based, full-stack procedural simulator and role-playing playground. Built on a containerized React 18+ / Vite / Express architecture, the applet operates as a single-view, highly polished interactive sandbox that models a serene island town. It utilizes advanced procedural generation algorithms to synthesize unique maps, dynamic weather-based canvas particle systems, and real-time side-scrolling parallax navigation.
Crucially, the platform leverages server-side Gemini AI models (specifically gemini-3.1-flash-lite) to power dynamic, contextual, and immersive gameplay mechanics. This specification outlines the complete engineering details of the system—from compilation and runtime parameters to procedural algorithms, database schema designs, canvas physics engines, and the deep implementation details of three new "WOW" AI-driven systems.
2. System Architecture Overview
2.1 Full-Stack Containerized Architecture
The application runs inside a secure, sandboxed Cloud Run container environment. This environment imposes strict requirements on network routing, process lifetime, and asset compiling.
code
Code
+-------------------------------------------------------------+
       |                  NGINX REVERSE PROXY LAYER                  |
       |  - Routes external traffic exclusively on Port 3000         |
       |  - Offloads SSL/TLS and enforces HTTP/1.1 or HTTP/2        |
       +------------------------------+------------------------------+
                                      |
                                      v
       +-------------------------------------------------------------+
       |             EXPRESS NODE.JS WEB SERVER (Port 3000)          |
       |  - Handles API requests (/api/*)                            |
       |  - Executes server-side Gemini API SDK interactions         |
       |  - Serves static compiled frontend assets in Production     |
       |  - Mounts Vite Development Middleware in Dev Mode           |
       +------------------------------+------------------------------+
                                      |
                     +----------------+----------------+
                     |                                 |
                     v                                 v
       +---------------------------+     +---------------------------+
       |      VITE DEV ENGINE      |     |  COMPILED FRONTEND ASSETS |
       |  - Injects hot wrappers   |     |  - Serves index.html      |
       |  - Bypasses CJS lookups   |     |  - Serves dist/assets/*   |
       |  - Runs in dev mode       |     |  - Runs in prod mode      |
       +---------------------------+     +---------------------------+
2.2 Compilation and Bundling Engine
To prevent runtime ES module compatibility issues common with Node.js on varied cloud platforms, the backend is bundled using esbuild. The frontend uses Vite's production pipeline.
Vite Build Phase (npm run build): Vite transpiles and bundles the React components, Tailwind CSS styling, and asset references into a highly optimized, minified folder at /dist.
Backend Compile Phase: The TypeScript entry file server.ts is compiled to CommonJS via:
code
Bash
esbuild server.ts --bundle --platform=node --format=cjs --packages=external --sourcemap --outfile=dist/server.cjs
Execution Phase (npm run start): The Node.js process executes node dist/server.cjs. This unified CJS format avoids relative path resolution issues at runtime, ensuring robust, high-performance execution.
2.3 Sandboxing and Ingress Routing Constraints
The infrastructure forces the applet to run on Port 3000 with the host configured to 0.0.0.0 for proper container ingress routing. Any attempt to use alternative ports will result in connection timeouts from the outer reverse proxy.
3. Database Schema & State Management
The architecture is designed to support both highly fast, transient client-side states and a highly scalable, persistent backend layer powered by Firebase Firestore. Below is the precise architectural blueprint for persistent database collections, combined with client-side state models.
3.1 Persistent Database Blueprint (Firestore Schema)
code
Code
+--------------------+
             |      Users         |
             |  - userId (PK)     |
             |  - email           |
             |  - bells           |
             |  - petals          |
             +---------+----------+
                       |
                       | 1:1
                       v
             +--------------------+
             |      Islands       |
             |  - islandId (PK)   |
             |  - userId (FK)     |
             |  - name            |
             |  - seed            |
             |  - topology        |
             +---------+----------+
                       |
                       | 1:N
                       v
             +--------------------+           +--------------------+
             |     Villagers      |           |   Relationships    |
             |  - villagerId (PK) | 1:N   1:N |  - relId (PK)      |
             |  - name            +---------->+  - userId (FK)     |
             |  - personality     |           |  - villagerId (FK) |
             |  - catchphrase     |           |  - score           |
             +--------------------+           +--------------------+
The Firestore schema contains five primary document collections:
users: Manages player profiles, high-level credentials, currency metrics (Bells), and unlock tokens.
userId (string, Document ID): Unique identifier.
displayName (string): The custom name of the Resident.
bells (number): The total gold standard wallet size.
petals (number): Dynamic currency utilized for eco-based purchases.
activeOutfit (string): Current styling choice.
hat (string): Equipped hat.
accessory (string): Equipped accessory.
equipment (string): Tool currently gripped in hand (e.g., 'net', 'rod', 'shovel', 'none').
islands: Tracks structural, physical parameters of the town.
islandId (string, Document ID): Unique identifier.
userId (string): FK pointing to the owning player.
name (string): Visual name of the location.
seed (string): Textual baseline key used in procedural equations.
topology (string): Enumerated layout types: 'meadow', 'coast', 'atoll'.
waterDensity (string): River flow modifiers: 'low', 'medium', 'high'.
treeDensity (string): Vegetation spawning frequency.
flowerDensity (string): Decorative flora spawning density.
villagers: Houses base records of local non-player character entities.
villagerId (string, Document ID): Key (e.g. 'v_nook', 'v_isabelle', 'v_bob', 'v_blathers').
name (string): Character name.
species (string): Animal classification.
personality (string): Mood pattern (e.g., Snooty, Lazy, Jock).
catchphrase (string): Appended tag.
avatar (string): Emojis or asset coordinates.
relationships: Pivot collection linking players to villagers to track relationship values.
relationshipId (string, Document ID): Composite unique key.
userId (string): Link to user.
villagerId (string): Link to villager.
score (number): Progression counter bound from 0 to 100.
friendshipStage (string): Evaluated milestone stage: 'Stranger', 'Buddy', 'Bestie', 'Soulmate'.
captured_items: Logs fish, bugs, fossils, and custom furniture objects stored in the backpack.
itemId (string, Document ID): Unique asset ID.
userId (string): Target player's inventory list.
name (string): Human-readable name.
type (string): Classification: 'bug', 'fish', 'fossil', 'furniture'.
price (number): Resale valuation in Bells.
origin (string): Descriptive capturing lore.
3.2 Client-Side State & TypeScript Interface Definitions
To enforce strict compile-time types, the frontend employs a central types file /src/types.ts. It models tiles, active inventory, logs, villagers, and map brushes:
code
TypeScript
export interface GameItem {
  id: string;
  type: 'tree' | 'flower' | 'rock' | 'balloon';
  name: string;
  variant: string;
  color?: string;
}

export interface Villager {
  id: string;
  name: string;
  species: string;
  personality: string;
  catchphrase: string;
  avatar: string;
  x: number;
  y: number;
  relationship?: number;       // Relationship progress (0-100)
  friendshipStage?: string;    // Stranger, Buddy, Bestie, Soulmate
}

export interface Tile {
  type: 'grass' | 'path' | 'water' | 'sand';
  item?: GameItem | null;
  villager?: Villager | null;
}

export interface PlayerCharacter {
  name: string;
  outfit: string;
  hat: string;
  accessory: string;
  equipment: 'none' | 'net' | 'rod' | 'shovel';
  gender?: string; // Used to track Cozy Vibe Personality modifiers
}

export interface ActivityLog {
  id: string;
  timestamp: string;
  type: 'general' | 'bug' | 'fish' | 'fossil' | 'ai' | 'diy';
  message: string;
}

export type MapBrushType = 'grass' | 'path' | 'water' | 'sand' | 'tree' | 'flower' | 'rock' | 'villager' | 'eraser';
4. Procedural Map Studio & Generation Algorithms
The application generates terrain dynamically. Rather than saving large, static coordinate tables, the engine relies on seeded generation. This guarantees that inputting a specific seed string generates the exact same topological configuration every time.
code
Code
+------------+       +-------------------------+       +---------------------------+
| Seed Input | ----> |  Seeded Hash Generator  | ----> |  Normalized Deterministic |
|  "atoll"   |       |  (sin/cos shift method) |       |      Random Values        |
+------------+       +-------------------------+       +-------------+-------------+
                                                                     |
                                                                     v
                                                       +-------------+-------------+
                                                       |   Topology Selection      |
                                                       +-------------+-------------+
                                                                     |
                           +-----------------------------------------+-----------------------------------------+
                           |                                         |                                         |
                           v                                         v                                         v
             +---------------------------+             +---------------------------+             +---------------------------+
             |    Meadowland Topology    |             |    Sandy Coast Topology   |             |    Atoll Lagoon Topology  |
             | - Row 5 path              |             | - Water columns (0-2)     |             | - Distance formula r, c   |
             | - Grass land default      |             | - Sand columns (3-4)      |             | - Sand ring (r=3.2 - 4.5) |
             | - River streams           |             | - Grass land rest         |             | - Grass center (r < 3.2)  |
             +---------------------------+             +---------------------------+             +---------------------------+
4.1 Pseudo-Random Number Generation (PRNG) via Hash Extraction
Standard JavaScript Math.random() cannot be seeded. Instead, the engine implements a lightweight, deterministic hashing generator based on the sine-wave offset extraction technique.
code
TypeScript
// Deterministic hash creation from a string seed
let hash = 0;
for (let i = 0; i < seed.length; i++) {
  hash = seed.charCodeAt(i) + ((hash << 5) - hash);
}

// Function to pull a fractional value between [0.0, 1.0) based on grid coordinates
const getRand = (offset: number) => {
  const val = Math.sin(hash + offset * 123.456);
  return (val - Math.floor(val));
};
Using this approach, we feed the generator a combined key of the tile's coordinates 
 plus a specific unique offset multiplier. This returns a consistent, pseudo-random float value between 
 and 
.
4.2 Coastal Contour and Shape Formulation
The physical outline of the map is defined by three selectable topological equations:
1. Meadowland Valley
A central path splits the meadow horizontally. A river intersects the terrain vertically based on the selected water density.
\text{TileType}(r,c) = \begin{cases}
\text{path}, & \text{if } r = 5 \
\text{water}, & \text{if } c \in [10, 11] \quad (\text{Medium Water density}) \
\text{grass}, & \text{otherwise}
\end{cases}
2. Sandy Coastline Border
This topology creates a beach along the left side of the screen, with the ocean extending to the far left.
\text{TileType}(r,c) = \begin{cases}
\text{water}, & \text{if } c \le 2 \
\text{sand}, & \text{if } c \in [3, 4] \
\text{grass}, & \text{otherwise}
\end{cases}
3. Atoll Lagoon Ring
An organic, circular lagoon centered on the map.
Let the center of the grid be 
.
For each tile at coordinates 
, we calculate the Euclidean distance 
 to the center:

\text{TileType}(r,c) = \begin{cases}
\text{water (outer ocean)}, & \text{if } d > 4.5 \
\text{sand (atoll ring)}, & \text{if } 3.2 < d \le 4.5 \
\text{grass (lagoon center)}, & \text{if } d \le 3.2
\end{cases}
4.3 Resource Density Probabilities
Spawning rates for natural resources (fruit trees, cosmos flowers, buried stardust rocks) are controlled by scaling density variables (low, medium, high) into distinct probability thresholds.
code
TypeScript
const treeChance = treeDensity === 'low' ? 0.05 : treeDensity === 'medium' ? 0.12 : 0.25;
const flowerChance = flowerDensity === 'low' ? 0.05 : flowerDensity === 'medium' ? 0.15 : 0.32;
For each coordinate pair where the terrain type is 'grass', the engine extracts a deterministic random value:
If 
: A fruit tree spawns.
If 
: A rose, violet, or cosmos flower spawns.
If 
: A rock containing a buried fossil spawns.
5. Canvas Rendering & Parallax Side-scroller Engine
The game rendering engine uses an HTML5 Canvas wrapped in a responsive React state container. It features multi-layer parallax scrolling and realistic real-time weather effects.
5.1 The Rendering Lifecycle Loop
The canvas runs on a standard requestAnimationFrame loop. It monitors screen resizes using a ResizeObserver, ensuring the rendering stays crisp and adapts dynamically without stretching.
code
TypeScript
const render = () => {
  const ctx = canvas.getContext("2d");
  if (!ctx) return;
  
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  
  // 1. Render Sky & Sun/Moon light based on Active weather state
  drawEnvironmentSky(ctx);
  
  // 2. Render Background Distant Hills (Parallax factor: 0.1)
  drawHillsLayer1(ctx, bgOffset);
  
  // 3. Render Foreground Hills (Parallax factor: 0.3)
  drawHillsLayer2(ctx, bgOffset);
  
  // 4. Render Main Ground Floor (Parallax factor: 1.0)
  drawMeadowFloor(ctx, bgOffset);
  
  // 5. Draw Active Map Sprite Assets and Characters
  drawActiveEntities(ctx, bgOffset);
  
  // 6. Draw Animated Weather Particle Overlay (Rain/Snow)
  drawWeatherOverlay(ctx);
  
  animFrameId = requestAnimationFrame(render);
};
5.2 Dynamic Parallax Background Layers
The background layers adjust their rendering offsets relative to the main world camera offset (bgOffset), creating a sense of depth:
Sky Gradient: Standard linear color maps transition smoothly between weather states and day/night settings.
Distant Hills (Offset: 
): Slowly panning green and slate gray curves.
Foreground Hills (Offset: 
): Mid-size mounds rendered in rich greens or snow-laden slate grays.
Meadow Ground (Offset: 
): The main action layer. This represents the viewport row where the player walks, shakes trees, catches bugs, or chats with villagers.
5.3 Weather Particle Physics and Particle Oscillators
The weather engine features dynamic, real-time particle calculations for rain and snow:
code
Code
[ CLOUDY OVERCAST SKY LAYER ]
             =================================
               |      |      |      |      |
               |      \      |      \      |    <-- Rain: Slanted lines falling fast
               \      |      \      |      \        (y_vel = 0.8px/ms, x_vel = -0.3px/ms)
               |      \      |      \      |
               v      v      v      v      v
              *        *       *       *        <-- Snow: Sinusoidal drifting flakes
            *   *    *   *   *   *   *   *          (y_vel = 0.12px/ms, x = offset + sin(y))
Rain Particle Vector Math
Raindrops are drawn as thin, translucent slanted lines falling at a high velocity:
Vertical velocity: 
Horizontal sweep: 

Snow Particle Drift Mechanics
Snowflakes drift down gently, swaying side-to-side using a sinusoidal horizontal wave function:
Vertical drift speed: 
Horizontal swing: 

5.4 Character Customization & Hand Tool Sprite Overlays
The player's character is drawn at a fixed position on the screen, with active clothing and accessory customizations applied on top:
Wardrobe Base: The system renders a custom player outfit sprite (e.g., Green Leaf Tee, K.K. Slider Fan Tee, Flannel Adventure Jacket) over the base character model.
Hat Sprite Offset: Straw hats or red caps are dynamically placed above the head coordinate:
Accessory Offset: Glasses, scholarly monocles, or cozy cheek bandages are drawn centered on the face:
Hand Tools Action Offset: When the player holds a tool (Net, Fishing Rod, or Shovel), a custom 16px utility sprite is rendered in their hand. Pressing Spacebar triggers an animation that swings the tool down in a smooth arc.
6. Detailed API Interface Contracts & Proxy Layer
The applet enforces secure API practices by running all Gemini SDK queries on the server side. Sensitive API keys are never exposed to the client.
code
Code
+--------------------+              +--------------------+              +--------------------+
|   Client Browser   |  (HTTP POST) |   Express Proxy    |  (SDK Call)  |     Gemini API     |
|  /api/gemini/chat  +------------->+    (server.ts)     +------------->+   models/gemini-   |
|   (User Message)   |              |  - Inject System  |              |   3.1-flash-lite   |
|                    |              |    Prompts & Key   |              |                    |
+--------------------+              +--------------------+              +--------------------+
The Express backend exposes six endpoints. These route calls to the @google/genai TypeScript SDK, using strict system instructions to guarantee structured JSON output.
6.1 Chat API Contract (/api/gemini/chat)
Engages the player in a simulated conversation with a selected villager, keeping their personality traits and local catchphrases intact.
Request Method: POST
Content-Type: application/json
JSON Request Body:
code
JSON
{
  "villager": "Tom Nook",
  "history": [
    { "sender": "Aveline", "text": "Can I pay off my home loan installment in turnips?" }
  ],
  "message": "Please, yes? Or do I need to sell some island fruit?",
  "modelName": "gemini-3.1-flash-lite"
}
Server-Side System Instructions Injected:
"You are Tom Nook, the entrepreneurial raccoon businessman from Animal Crossing. Your personality is focused on loans, construction, and business opportunities, but you are still warm and cozy. Always append your catchphrase 'yes, yes' naturally. Return your response in a simple JSON format containing a single 'reply' string."
JSON Response Body:
code
JSON
{
  "reply": "Well, yes, yes! Turnips are certainly valuable, but I highly recommend selling some delicious local peaches at the Resident Services shop to guarantee your next loan installment! Yes, yes!"
}
6.2 K.K. Slider Song API Contract (/api/gemini/kkslider)
Creates custom, themed songs complete with retro whistle arrangements, lyrics, and guitar chords.
Request Method: POST
Content-Type: application/json
JSON Request Body:
code
JSON
{
  "prompt": "Walking through the fallen leaves at sunset",
  "genre": "Nostalgic Folk",
  "modelName": "gemini-3.1-flash-lite"
}
JSON Response Body:
code
JSON
{
  "songTitle": "K.K. Maple Walkabout",
  "lyricsWithChords": "[C] Golden leaves fall on [F] the ground,\n[G] Not a single busy [C] urban sound.\n[Am] Walk along the sandy [Dm] coast so sweet,\n[G] Soft red stardust under [C] our feet.",
  "albumArtDescription": "An oil painting of K.K. Slider sitting under a cozy, glowing maple tree, strumming an acoustic wood guitar while colorful red leaves drift around him.",
  "whistleTune": ["C", "E", "G", "C2", "B", "A", "G", "-", "F", "A", "C2", "A", "G", "E", "C", "-"],
  "kkQuote": "You know, when the autumn wind rolls in, there's nothing better than walking slow and letting your thoughts drift, man."
}
6.3 Custom DIY Designer API Contract (/api/gemini/diy-designer)
Drafts custom furniture blueprints based on gathered resources and player requests.
Request Method: POST
Content-Type: application/json
JSON Request Body:
code
JSON
{
  "materials": ["Wood 🪵", "Peach 🍑", "Clamshell 🐚"],
  "prompt": "A modern beachfront desk with a custom fruit drawer",
  "modelName": "gemini-3.1-flash-lite"
}
JSON Response Body:
code
JSON
{
  "itemName": "Peach Driftwood Study Desk",
  "description": "An elegant study table carved from salt-washed driftwood, featuring sweet-scented peach drawer knobs and a polished abalone clam shell coin tray.",
  "bellValue": 2400,
  "requiredMaterials": "4x Driftwood, 2x Fresh Peaches, 3x Polished Clamshells",
  "decorationPlacement": "Indoor Furniture or Sandy Beachfront Outdoor accent"
}
6.4 Item Evaluation API Contract (/api/gemini/evaluate-item)
Evaluates fossils or custom findings. Blathers provides scholarly insight, while Tom Nook evaluates the item's economic resale value.
Request Method: POST
Content-Type: application/json
JSON Request Body:
code
JSON
{
  "itemName": "Iridescent Starry Ammonite",
  "itemDesc": "A deeply swirled prehistoric shell that glows with soft purple cosmic stardust.",
  "category": "fossil",
  "modelName": "gemini-3.1-flash-lite"
}
JSON Response Body:
code
JSON
{
  "character": "Blathers",
  "evaluation": "By Jove! This magnificent specimen is an Iridescent Starry Ammonite! It dates back to the early Mesozoic era. The luminous starry residue suggests it was preserved near an ancient meteorite impact! Simply breathtaking!",
  "bellValue": 4500,
  "diyRecipe": "Starry Ammonite Fossil Pedestal Stand",
  "funFact": "Ammonites are ancient cephalopods that are actually more closely related to modern octopuses and squids than to the shelled snails they resemble!"
}
6.5 Katrina's Fortune API Contract (/api/gemini/katrina)
Provides astrological horoscopes and dream island descriptions based on the player's recent activities.
Request Method: POST
Content-Type: application/json
JSON Request Body:
code
JSON
{
  "category": "friendship",
  "vibe": "Shaking trees and sharing fresh peaches with Bob the cat",
  "modelName": "gemini-3.1-flash-lite"
}
JSON Response Body:
code
JSON
{
  "fortune": "The cosmic stardust reveals that sharing sweet peaches opens the gateway to eternal friendship. Your zodiac star aligns with the celestial retriever!",
  "luckyItem": "Cozy Pink Straw Hat",
  "luckyNumber": 7,
  "luckyColor": "Pastel Peach Pink",
  "dreamIslandName": "Aura Cascade Island",
  "dreamIslandDescription": "A dream island where fresh peaches grow as large as houses and sweet rivers glow with stardust. Bob the cat is seen resting on a giant leaf hammock."
}
7. Three Additional WOW AI Features: In-Depth Breakdown
To enhance the simulator experience, the system implements three unique, AI-driven mechanics. These leverage structured JSON parsing to drive responsive, real-time gameplay.
code
Code
=============================================================
                          AI ENGINE CAPABILITIES
       =============================================================
       
         [ WOW FEATURE 1: KATRINA'S FORTUNE TELLING & ASTROLOGY ]
         --> Inputs: Vibe, Category (Wealth, Love, Friendship)
         --> Outputs: Horoscopes, Lucky Numbers, Dream Island Lore
         
         [ WOW FEATURE 2: K.K. SLIDER folk WHISTLE SYNTHESIZER ]
         --> Inputs: Theme prompt (e.g., Autumn Rain)
         --> Outputs: Chords, Lyrics, and Web-Audio pitch notes array
         
         [ WOW FEATURE 3: TOM NOOK'S CUSTOM DIY BLUEPRINT WORKBENCH ]
         --> Inputs: Selected Materials inventory, Style request
         --> Outputs: Unique Items added directly to Backpack
7.1 WOW Feature 1: Katrina's Astral Horoscope & Dream Suite Generator
This feature adds Katrina, the mysterious fortune-telling panther, to the AI Panel. Players can select a focus category (Wealth, Love, Friendship, Health) and type in a brief description of their recent activities.
Underlying Mechanics
The user's input is sent to the /api/gemini/katrina endpoint.
The model generates a whimsical horoscope in the voice of Katrina, along with a "Lucky Item," "Lucky Number," "Lucky Color," and a descriptive "Dream Island."
The frontend parses the response and updates the UI with starry visual effects. If the player is currently wearing the "Lucky Item" or "Lucky Color," the system triggers a surprise bonus (e.g., spawning rare golden butterflies or doubling their next tree-shaking reward).
7.2 WOW Feature 2: K.K. Slider's Retro Web-Audio Folk Whistle Synthesizer
This feature brings K.K. Slider to life in a fully playable way. In addition to generating custom lyrics and chord sheets, the AI generates a retro-style whistle tune as an array of pitch variables.
Web Audio API Sound Generation
When a player clicks Play Live Whistle, the engine initializes an instance of the browser's AudioContext. It loops through the pitch array generated by the AI, translating notes to their exact frequencies:
Note Notation	Frequency (Hz)	Note Notation	Frequency (Hz)
C	
A	
D	
B	
E	
C2	
F	
D2	
G	
E2	
-	Rest (0)	z	Mute (-1)
The engine schedules the synthesizer using a smooth, retro-style whistle waveform (type: "sine"), applying a dynamic volume envelope to make each note sound warm and natural:
code
TypeScript
const playKkTune = () => {
  const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  
  // Note scheduling loop (400ms intervals)
  const osc = audioCtx.createOscillator();
  const gainNode = audioCtx.createGain();
  
  osc.type = "sine";
  osc.frequency.setValueAtTime(frequencyMap[note], audioCtx.currentTime);
  
  // Volume Envelope: Prevent audio popping
  gainNode.gain.setValueAtTime(0.15, audioCtx.currentTime);
  gainNode.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.35);
  
  osc.connect(gainNode);
  gainNode.connect(audioCtx.destination);
  osc.start();
  osc.stop(audioCtx.currentTime + 0.4);
};
This feature turns textual prompts into real, synthesized retro music inside the browser, recreating the feel of K.K. Slider performing live in the town square.
7.3 WOW Feature 3: Tom Nook's Algorithmic Material DIY Design Drafter
This feature adds an interactive crafting engine directly into the gameplay loop. Players choose materials from their inventory (e.g., Wood, Peaches, Fossils, Flowers) and enter a styling prompt.
Implemented Crafting Mechanics
The applet sends the selected materials and prompt to the /api/gemini/diy-designer endpoint.
The AI generates a customized, themed item.
The frontend parses the item's name and value, adding it directly to the player's backpack as a crafted piece of furniture:
code
TypeScript
setCapturedItems(prev => [
  {
    name: `${data.itemName} (Custom DIY)`,
    type: "furniture",
    price: data.bellValue,
    origin: `Crafted using: ${selectedMaterials.join(", ")}`
  },
  ...prev
]);
This bridges the AI features directly with the game's economy, letting players sell their custom creations to Tom Nook for Bells to help pay off their house loans.
8. Bug Fixes, Refinement History & Linter Resolutions
During development, several critical structural bugs and type issues were identified and resolved to ensure a stable build.
8.1 Friendship/Gift Log Categories Mismatch
Symptom: The TypeScript compiler reported errors when attempting to log villager interactions using 'friendship' or 'gift' as categories.
code
Code
src/App.tsx(1455,121): error TS2345: Argument of type '"friendship"' is not assignable to parameter of type '"fossil" | "bug" | "fish" | "general" | "ai" | "diy"'.
Root Cause: The type signature for ActivityLog explicitly restricted log types to general, bug, fish, fossil, ai, or diy. Using arbitrary string categories was invalid.
Resolution: Remapped friendship progression updates and gift-giving actions to the 'general' category, while enriching the log message with specific details and formatting. This resolved the type mismatch and kept the activity feed neat.
8.2 Component Prop Interface Alignment (Dashboard and InventoryModal Sync)
Symptom: Inconsistent prop interfaces caused type errors during compilation.
code
Code
src/App.tsx(2157,19): error TS2322: Type '{ bells: any; ... }' is not assignable to type 'DashboardProps'.
Root Cause: The Dashboard component expected separate stat counts for bugs, fish, and fossils, along with the map grid, but the initial parent implementation passed the raw logs array. The InventoryModal component also had mismatching prop keys (expecting capturedItems and onSellItem, but receiving items and onGiveGift).
Resolution: Standardized the prop interfaces. The parent parameters in App.tsx were updated to match the components' specifications exactly:
Correct Dashboard Integration
code
TypeScript
<Dashboard 
  grid={grid}
  villagers={villagers}
  capturedItems={capturedItems}
  bells={bells} 
  petals={petals} 
  bugsCount={bugsCaught}
  fishCount={fishCaught}
  fossilsCount={fossilsDug}
  balloonCount={balloonHits}
  lang={lang} 
/>
Correct Inventory Modal Integration
code
TypeScript
<InventoryModal
  isOpen={isInventoryOpen}
  onClose={() => setIsInventoryOpen(false)}
  capturedItems={capturedItems}
  onSellAll={handleSellAllItems}
  onSellItem={handleSellItem}
  bells={bells}
  lang={lang}
/>
These changes resolved all linter warnings, resulting in a clean, successful build.
9. UI/UX Visual Design & Typography Systems
The interface is styled with a custom Tailwind CSS configuration designed to look organic and polished, avoiding generic presets.
code
Code
==============================================================
                          THEMATIC PALETTES
       ==============================================================
       
         [ WARM SUNNY DAY THEME ]
         --> Background: Cozy Pastel Ivory (#FFFCEE)
         --> Primary Container: Warm Sand Cream (#F8F8D8)
         --> Borders: Forest Green Accent (#8BBD8B)
         --> Text Color: Earthy Wood Charcoal (#5D4037)
         
         [ COZY STARLIGHT NIGHT THEME ]
         --> Background: Deep Indigo Blue (#0F172A)
         --> Primary Container: Obsidian Slate (#1E293B)
         --> Borders: Lavender Indigo (#6366F1)
         --> Text Color: Silver Gray Cream (#F1F5F9)
9.1 Styling Tokens
The visual design is built around clear, intentional aesthetic choices:
Typography: Clean, geometric Inter is used for the UI elements, while JetBrains Mono is used for metrics and system logs to keep technical data readable and structured.
Warm Sunny Day Theme: Designed to feel warm and relaxing.
Base Background: bg-[#FFFCEE]
Secondary Accents: bg-[#F8F8D8]
Soft Borders: border-[#8BBD8B]
Primary Text: text-[#5D4037]
Cozy Starlight Night Theme: An elegant dark mode that turns the island into a calm, nighttime escape.
Base Background: bg-slate-900
Secondary Accents: bg-slate-800
Deep Borders: border-slate-700
Primary Text: text-slate-100
9.2 Mobile-First Responsive Design Constraints
To ensure a great experience on any device, the layout is fully responsive:
Backpack Overlay: The inventory uses a modal drawer on smaller screens (max-w-md mx-auto) to save space, but expands into a persistent grid view on larger desktops.
Tactical Grid: The building grid scales down on mobile viewports using a CSS flex wrap, keeping the grid cells at a touch-friendly size of at least 44px on smartphones.
Double Column Splitting: On smaller displays, the sidebar controls stack neatly below the main canvas. On larger screens, the layout automatically splits into a spacious 12-column grid layout:
Resident Wardrobe & Gear selection: lg:col-span-3
Active Parallax Sidescroller Canvas: lg:col-span-6
Immersive AI Interactions Panel: lg:col-span-3
10. Comprehensive Follow-Up Questions
To help guide future expansion and refine this specification, we have compiled 20 structured technical questions:
Procedural Seed Scaling: How should the Pseudo-Random Number Generator (PRNG) be updated if we expand the map beyond the standard grid size, to prevent visual tiling artifacts?
Local Storage Synchronization: How should local changes to a player's inventory be merged with the cloud database when reconnecting after being offline?
Canvas Performance Profiling: How can we optimize the canvas rendering loop when drawing a high number of active weather particles? Is it worth using an offscreen canvas buffer?
Collision Detection System: Should we add a box-collider system to the side-scroller layout to prevent the player from walking into river tiles or structures?
Dynamic Weather Logic: How should we update the weather cycle? Should we tie it to actual real-world weather patterns using a third-party weather API?
Gemini Prompt Guardrails: What steps should we take to prevent players from bypassing the AI's personality parameters (prompt injection) during chat sessions?
Bells & Items Balanced Economy: How can we balance item values and house loan costs as more procedurally generated furniture items are added to the game?
Friendship Level Scaling: What formula should we use to scale friendship progress? How can we make higher levels more challenging to reach?
Interactive Mini-Games: How can we use the canvas physics engine to create interactive mini-games, like timing fish bites or catching bugs?
Custom Furniture Assets: Should we allow players to upload custom vector patterns to decorate their procedurally generated furniture?
Web Audio Sound Effects: How can we expand the Web Audio API engine to synthesize other instanced sounds, like gentle river water or rain drops?
Firestore Schema Performance: How will our Firestore collection structure scale as the number of active players grows? Will we need composite database indexes?
Vite Bundling Optimization: How can we split the main JS bundles to make sure the applet loads quickly, even on slower mobile networks?
Accessibility System (WCAG): What color palette adjustments do we need to make to ensure high-contrast accessibility in both Light and Dark themes?
KK Slider Chords Interpretation: Can we build a visual chord diagram generator that shows players how to play K.K. Slider's AI songs on a real ukulele or guitar?
Multiplayer Room Sync: If we add real-time multiplayer, should we use Firebase WebRTC or persistent WebSockets to coordinate player positions?
Seeded Biome Variations: How can we update the map generator to support distinct biomes (such as a desert island or a cherry blossom forest) using seed modifiers?
Custom Town Flag Painter: Can we add a pixel-art canvas editor that lets players design their own town flag and wear it as an outfit?
Automated AI Testing: What is the best way to write automated unit tests to verify that the Gemini API endpoint always returns valid, correctly structured JSON payloads?
Long-Term Storage Archival: How should we manage and archive historical event logs in the ledger without causing slow database reads?
