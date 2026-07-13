TOUR DE FRANCE 2026: LIVE SIMULATOR & TACTICAL STORYBOARD
Full-Stack Enterprise Technical Specification and Interactive AI Blueprint
CHAPTER 1: Executive Summary & Architectural Overview
The Tour de France 2026 Live Simulator & Interactive Storyboard is an immersive, highly scalable, and full-stack web application designed to bring the tactical complexity, physical rigor, and rich narratives of Grand Tour cycling to life. Operating at the intersection of real-time sports telemetry, multi-perspective spatial routing, and generative artificial intelligence, this application transforms raw physical parameters into narrative-rich athletic drama.
The core objective of the platform is to allow cycling enthusiasts, amateur tacticians, and sports analysts to sandbox tactical race strategies. Users can select real-world or virtual riders, team bikes, and scheduled 2026 routes, projecting their capabilities across the iconic climbs and treacherous winds of the French landscape.
System Overview
At its heart, the application leverages a unified, full-stack, decoupled architecture utilizing React 19 and Vite on the client, and a lightweight, high-performance Express server in the backend.
Narrative Simulation: The system feeds structural state representations (rider attributes, team profiles, and geographical milestones) into Google's Gemini AI Engine to generate highly contextual, multi-part sports broadcasts and tactical briefs.
Web-Search Grounding: To solve the static-database limitation of typical sports simulators, the application features a search-grounded state-recovery loop. It uses the modern @google/genai SDK with active search tools to query the live web, pulling in current standings, real-world rider status, and live stage developments as they occur throughout July 2026.
Tactical Storyboard Replays: A state-driven timeline component replays key mechanical and physical developments across specific stages, displaying road slope, gap times, group compositions (such as breakaways, chase groups, and the gruppetto), and synchronized audio transcripts.
Spatial Visualization: A specialized component provides five distinct viewpoints (Elevation, 3D topographical trace, Microclimates, Team Bus Chalkboard, and Fan Density) to let users analyze the stage from every angle.
Technological Philosophy
This application operates on a strict server-client division. All API keys, search invocations, and generative prompts remain securely encapsulated within the Express backend. This protects sensitive infrastructure credentials while serving polished, fast JSON/text responses to the browser. The frontend remains visual, responsive, and highly animated, utilizing Tailwind CSS utility classes and Lucide-React vector graphics to build a premium, publication-grade dark interface.
CHAPTER 2: Full-Stack System Topology
The execution topology is optimized for fast, local development sandboxing and production deployment in containerized environments like Google Cloud Run. The network operates under strict single-port routing, mapping all traffic through port 3000.
code
Code
+------------------------------------------------------------------------------------------------+
  |                                   CLIENT (Vite SPA / React 19)                                 |
  |                                                                                                |
  |  +---------------------------+  +---------------------------+  +----------------------------+  |
  |  |    Campaign Sandbox       |  |   Live Replay Timeline    |  |     Multi-View Stage       |  |
  |  |    State Selector         |  |   & Commentary Log        |  |     Visualizer             |  |
  |  +-------------+-------------+  +-------------+-------------+  +-------------+--------------+  |
  |                |                              |                              |                 |
  +----------------|------------------------------|------------------------------|-----------------+
                   |                              |                              |
                   | (HTTP POST Requests)         | (Replay Seeds / frames)      | (Active stage props)
                   v                              v                              v
  +------------------------------------------------------------------------------------------------+
  |                                 SERVER (Express / Node.js)                                     |
  |                                                                                                |
  |   +----------------------------------------------------------------------------------------+   |
  |   | /api/search-results  ---> Triggers Google Search Grounded Standings (Gemini 3.5 Flash)  |   |
  |   | /api/custom-story    ---> Generates 4-part Custom Broadcasts (Gemini 3.5 Flash)        |   |
  |   | /api/tactical-director -> Computes Car 1 strategic advice based on rider/stage stats   |   |
  |   | /api/commentary-tts  ---> Synthesizes Radio Tour PCM audio (Gemini 3.1 TTS Preview)    |   |
  |   | /api/risk-simulator  ---> Generates structured safety/crash JSON scorecard metrics     |   |
  |   +----------------------------------------------------------------------------------------+   |
  |                                                                                                |
  +-------------------------------------------------+----------------------------------------------+
                                                    |
                                                    | (Secure API Authentication)
                                                    v
                                  +----------------------------------+
                                  |     GOOGLE GEN AI PLATFORM       |
                                  |                                  |
                                  |  - gemini-3.5-flash (Grounding)   |
                                  |  - gemini-3.1-pro-preview        |
                                  |  - gemini-3.1-flash-tts-preview  |
                                  +----------------------------------+
Client Dev and Production Routing
Development: Vite mounts a development server on port 3000. In development mode, Vite intercepts asset requests, compile-on-demand files, and Hot Module Replacement triggers. The custom Express server hosts Vite’s middleware (vite.middlewares), routing client-side requests seamlessly while exposing /api/* endpoints directly on the same host and port.
Production: When compiled via npm run build, Vite bundles all React assets into static files in the /dist directory. The Express server detects NODE_ENV === "production", serving static assets from /dist via express.static() and routing fallback SPA lookups to /dist/index.html.
Environment Ingress & Network Binding
The backend is explicitly configured to bind to host 0.0.0.0 and port 3000. This host binding is required for standard container ingress routing, allowing reverse-proxy layers (like Nginx) to tunnel client traffic into the container.
CHAPTER 3: Data Schemas & Type System
To ensure strict type safety across the server-client boundary and prevent runtime errors during state updates, the application relies on a robust type definition system in src/types.ts. Below is an exhaustive breakdown of the core types and interfaces driving the simulator.
1. Player (Rider) Representation
Represents a professional cyclist, including their biographical data, team affiliation, strategic role, and normalized physiological/tactical stats.
code
TypeScript
export interface Player {
  id: string;
  name: string;
  nationality: string;
  teamId: string;
  role: 'GC Leader' | 'Climber' | 'Sprinter' | 'Domestique' | 'All-Rounder';
  stats: {
    climbing: number;   // Normalized 0-100 climbing performance
    sprinting: number;  // Normalized 0-100 raw muscular explosive speed
    timeTrial: number;  // Normalized 0-100 aerodynamic sustained solo wattage
    stamina: number;    // Normalized 0-100 grand-tour fatigue recovery rate
    tactics: number;    // Normalized 0-100 spatial reading & draft conservation
  };
  bio: string;
}
2. Team Representation
Defines the team identity, corporate sponsor structure, manufacturer components, and hex-color configurations.
code
TypeScript
export interface Team {
  id: string;
  name: string;
  country: string;
  bikeBrand: string;
  color: string;          // Hex code for primary jerseys (e.g., "#FFD166")
  secondaryColor: string; // Hex code for secondary frame details
}
3. Stage & Route Representation
Represents the spatial and geological parameters of a specific route, detailing categorized climbs, distance, terrain structure, and difficulty.
code
TypeScript
export interface Stage {
  number: number;
  date: string;
  start: string;
  finish: string;
  distanceKm: number;
  type: 'Flat' | 'Hilly' | 'Mountain' | 'Individual Time Trial' | 'Team Time Trial';
  difficulty: 'Easy' | 'Moderate' | 'Hard' | 'Extreme';
  climbs: string[];       // Array of climbs, e.g. ["Col du Tourmalet (HC)", "Col d'Aspin (Cat 1)"]
  description: string;
}
4. Replay Frame & Storyboard Representation
Defines a specific capture point along the race timeline, containing live visual state, group distances, and commentary.
code
TypeScript
export interface ReplayFrame {
  timestampKm: number;
  narrative: string;
  visualState: {
    leadGroup: string[];      // Names of riders currently in front
    chaseGroup: string[];     // Riders in the secondary pursuit pack
    peloton: string[];        // Standard group or general field representation
    gapSeconds: number;       // Temporal gap between Lead and Chase groups
    gradientPercent: number;  // Real-time asphalt gradient slope
    currentClimb?: string;    // Active climb name if on an ascent
  };
  commentaryTranscript: string;
}

export interface ReplayData {
  stageNumber: number;
  stageName: string;
  frames: ReplayFrame[];
  fullTranscript: string;
}
5. AI Configuration Types
Standardizes model definitions and paid-tier permissions across the app.
code
TypeScript
export interface AIModelConfig {
  id: string;
  name: string;
  description: string;
  isPaid: boolean;
}
CHAPTER 4: Interactive Sandbox & Selection Mechanics
The application features an interactive dashboard split into a responsive multi-column layout. The left column acts as the Campaign Sandbox, allowing the user to select the rider, team, and stage to simulate. This forms a reactive state machine in src/App.tsx.
code
Code
+-----------------------------------------+
                  |           User Action (UI)              |
                  |  - Select Rider (Tadej Pogačar)         |
                  |  - Select Team (UAE Team Emirates)      |
                  |  - Select Stage (Stage 9: Tourmalet)    |
                  +--------------------+--------------------+
                                       |
                                       v
                  +-----------------------------------------+
                  |          React Context State            |
                  |  - selectedPlayer: Player               |
                  |  - selectedTeam: Team                   |
                  |  - selectedStage: Stage                 |
                  +--------------------+--------------------+
                                       |
                   +-------------------+-------------------+
                   |                                       |
                   v                                       v
      +-------------------------+             +-------------------------+
      |  Custom Story Generator |             |   Wow AI Feature Hub    |
      |  Reflects new inputs   |             |   Re-triggers briefings  |
      |  instantly              |             |   & risk simulations    |
      +-------------------------+             +-------------------------+
State Coherency & Selector Architecture
When a user updates any dropdown (e.g., changing the rider to Remco Evenepoel), the component changes the active state, which flows down to child components.
Custom Story Generator: Clicking "Generate Custom Race Story" fires a POST request to /api/custom-story containing the selected rider, team, stage, and model metadata.
Dynamic UI Binding: The stats panel immediately adapts, changing from showing climbers' stats to showing time-trialists' values. This gives users immediate feedback before they even run a simulation.
Synchronized Background Triggers: Modifying the active rider or stage triggers a side-effect hook (useEffect) in WowFeatures.tsx. This automatically updates the Director Sportif Tactical Briefing and recalculates the Weather Risk Scorecard, keeping the sandbox experience completely synchronized.
CHAPTER 5: Component Deep Dive: Live Search Standings & Search Grounding Engine
The LiveSearchStandings component is a marquee feature of the application, resolving a common limitation of sports apps: outdated databases. By linking the Gemini API with active Google Search tools, the app brings real-time 2026 Tour de France updates directly into the UI.
code
Code
+------------------+       1. HTTP POST /api/search-results       +----------------------+
  |                  | -------------------------------------------> |                      |
  |  Client Component|                                              |  Express App Server  |
  |  (LiveSearch)    | <------------------------------------------- |  (server.ts)         |
  |                  |        4. Return Grounded Article JSON       |                      |
  +------------------+                                              +-----------+----------+
                                                                                |
                                                  2. Call generateContent       |
                                                     with googleSearch tool     |
                                                                                v
                                                                    +----------------------+
                                                                    |   Gemini 3.5 Flash   |
                                                                    |                      |
                                                                    |   1. Query Google    |
                                                                    |   2. Extract Results |
                                                                    |   3. Return Article  |
                                                                    +----------------------+
Grounded Search Implementation
The Express server implements search grounding by enabling the googleSearch tool within the @google/genai generate content call:
code
TypeScript
const response = await ai.models.generateContent({
  model: 'gemini-3.5-flash',
  contents: prompt,
  config: {
    tools: [{ googleSearch: {} }],
  },
});
The prompt instructs the model to act as an authentic sports anchor, summarizing general classification standings (Yellow, Green, Polka Dot, and White jerseys) and the latest three stages leading up to July 12, 2026 (Stage 7, 8, and today's Stage 9).
Client Rendering & Source Parsing
The server receives the grounded text, parses the metadata, and returns a list of web citations:
code
TypeScript
const chunks = response.candidates?.[0]?.groundingMetadata?.groundingChunks;
const sources = chunks ? chunks.map((c: any) => ({
  title: c.web?.title || 'Web Search Source',
  uri: c.web?.uri || '#',
})) : [];
The client maps these sources to clean anchor tags with a Link2 icon, letting users click through to verify the real-world articles.
To render the markdown safely without importing large external libraries, the client uses a custom parser:
Header Identification: Matches ### or #### prefixes and turns them into styled <h4> and <h5> elements with a bold gold color scheme (text-yellow-400 font-bold).
List Bullet Traversal: Matches * or - bullet marks, parses bold markdown bounds (**...**), and converts them into inline strong elements styled with high-contrast text.
Paragraph Wrapping: Handles line breaks and empty space, keeping the layout readable and structured.
CHAPTER 6: Component Deep Dive: Tactical Competition Replay Engine
The LiveReplay component features an interactive race playback system, mapping coordinates, slopes, and commentary across a multi-segment timeline.
code
Code
Sector 1 (KM 110) -------------> Sector 2 (KM 175) -------------> Sector 3 (KM 210)
   [Ascending Tourmalet]            [Valley descent]                  [Pau bunch sprint]
   - Gradient: +8.5%                - Gradient: -1.5%                 - Gradient: 0.0%
   - Lead: Vingegaard, Pogačar      - Lead: Alpecin leadout train     - Lead: Jasper Philipsen
   - Gap: 45 seconds                - Gap: 0 seconds                  - Gap: 1 second
Timing & Playback Orchestration
The playback state machine is driven by simple React states: currentFrameIndex (the current segment), isPlaying (whether active), and playSpeed (playback multiplier). An active interval hook controls frame transitions:
code
TypeScript
useEffect(() => {
  let timer: NodeJS.Timeout;
  if (isPlaying) {
    const intervalMs = 4000 / playSpeed; // default 4 seconds per sector, divided by speed
    timer = setInterval(() => {
      setCurrentFrameIndex((prevIndex) => {
        if (prevIndex < activeStageReplay.frames.length - 1) {
          return prevIndex + 1;
        } else {
          setIsPlaying(false); // End of track reached
          return prevIndex;
        }
      });
    }, intervalMs);
  }
  return () => clearInterval(timer);
}, [isPlaying, playSpeed, activeStageReplay.frames.length]);
Visualizing State Dynamics
Interactive Timeline Ticker: A custom SVG timeline track maps the rider's progress across the stage. A marker moves dynamically to reflect the current kilometer milestone (timestampKm).
Slope Gauge: A vertical indicator displays the current road gradient. It turns red for steep climbs (e.g., +10.1% on the Col d'Eze) and blue for fast descents, giving users an immediate visual feel for the terrain difficulty.
Group Simulation Board: Displays the live layout of the peloton. Riders in the lead group are styled with bold yellow backgrounds, the chase group is styled in orange, and the peloton remains in muted gray. This visual layout updates instantly as the simulation progresses.
Synchronized Commentary Feed: A scrolling sidebar displays a running transcript of the race, highlighting comments from "Radio Tour" at each key milestone.
CHAPTER 7: Component Deep Dive: Multi-Perspective Route Viewer
The RouteViewer component provides a deep-dive look at each stage, giving users a complete tactical picture through five distinct viewpoints.
code
Code
+-----------------------------------------------------------------------------+
  |                                ROUTE VIEWER                                 |
  |                                                                             |
  |  +------------+  +------------+  +------------+  +------------+  +-------+  |
  |  | Elevation  |  |  3D Map    |  | Microclim. |  | Chalkboard |  | Fans  |  |
  |  +------------+  +------------+  +------------+  +------------+  +-------+  |
  |                                                                             |
  |  +-----------------------------------------------------------------------+  |
  |  |                             ACTIVE CANVAS                             |  |
  |  |                                                                       |  |
  |  |   [Renders Viewpoint-Specific SVG, topographies, charts, or boards]   |  |
  |  |                                                                       |  |
  |  +-----------------------------------------------------------------------+  |
  +-----------------------------------------------------------------------------+
View 1: Elevation Profile
The elevation profile uses custom vector SVG drawing paths, adjusting the curve based on the stage difficulty:
Mountain Stages: Renders sharp peaks, marking major climbs (like the Col du Tourmalet) with red circle indicators.
Hilly Stages: Draws smooth waves, using orange warning marks to indicate punchy category 2 climbs.
Flat Stages: Draws a smooth, green path that stays close to sea level, highlighting a sprinters' paradise.
View 2: Topographical Tactical Trace (3D Perspective)
Provides an orthographic look at the race layout. Using a standard SVG path transformed via CSS 3D perspective (transform: perspective(400px) rotateX(45deg)), the map projects a 3D road visualization over an overlaid altitude grid. This showcases sharp corners, mountain descents, and start/finish flags.
View 3: Microclimates
Provides meteorological analytics for the active route:
Ambient Temp: Tracks valley heat, warning users about the risk of rider exhaustion on hot summer days.
Wind Gusts: Warns of strong coastal winds (e.g., 32 km/h), highlighting the risk of crosswinds splitting the peloton.
Summit Weather: Tracks colder mountain temperatures, warning teams about changing weather on high climbs.
Precipitation: Computes the probability of wet roads, letting users anticipate slick descents.
View 4: Team Bus Chalkboard
Simulates a sports director's tactical chalkboard, splitting the race plan into three distinct phases:
Phase 1 (Conserve): Form a tight draft shield, letting domestiques block headwinds and conserve the leader's energy.
Phase 2 (Attack Prep): Drop domestiques back to fetch fresh hydration bottles and ice, pacing the team before the climb.
Phase 3 (Decisive Attack): Launch a decisive acceleration on the steepest slopes, pushing high wattages to open a winning gap.
View 5: Fan Density Spectator Map
Renders a spectator distribution map across the stage:
Shows a low fan density in the neutral valleys.
Highlights high densities near sprint zones and extreme densities on major climb summits (up to 450,000 fans expected), where fans, paint-sprayed road slogans, and publicity caravans line the route.
CHAPTER 8: AI Engine & Prompt Engineering Matrix (The Three Original Wow Features)
All AI features connect through a unified prompt engineering interface, passing contextual parameters like rider stats, bike gear, and stage difficulty to build rich, immersive narratives.
code
Code
[Raw State Parameters]  ======================+
       - Rider stats (Climbing: 99, TT: 95)          |
       - Team component (Bike: Colnago, Color: Black)| ===> [Express Server API Endpoint]
       - Weather context (Windy, 32 km/h)            |
       - Stage climbs (Tourmalet, Aspin)             |
                                                     |
       [System Instructions & Specialized Prompts] ===+
       - Sports Anchor Tone, Cycling Jargon, JSON Schema
                                                     |
                                                     v
                                       [Google Gen AI Pipeline]
                                                     |
                                                     v
                                      [Polished Client Output]
                                      - Commentary PCM Audio
                                      - Strategic Briefing Markdown
                                      - Scorecard JSON Metrics
Feature 1: Director Sportif Tactical Briefing
API Endpoint: /api/tactical-director
Model: gemini-3.5-flash / gemini-3.1-pro-preview
Prompt Philosophy: Focuses on cycling jargon, acting as a sports director giving a high-stakes briefing from the team car. It integrates rider stats (like climbing weight) and stage terrain to map out exact attack zones.
Feature 2: Radio Tour Live Commentary Audio Generator (TTS)
API Endpoint: /api/commentary-tts
Model: gemini-3.1-flash-tts-preview
Audio Pipeline:
The backend connects directly to Gemini's Text-to-Speech engine, generating realistic radio commentary. It specifies audio modalities and selects a prebuilt voice config:
code
TypeScript
config: {
  responseModalities: ['AUDIO'],
  speechConfig: {
    voiceConfig: {
      prebuiltVoiceConfig: { voiceName: voice }, // 'Kore' (Male) or 'Puck' (Female)
    }
  }
}
The server converts the binary audio payload into a base64 string, which the client decodes and plays using a browser blob URL:
code
TypeScript
const binary = atob(data.audio);
const bytes = new Uint8Array(binary.length);
for (let i = 0; i < binary.length; i++) {
  bytes[i] = binary.charCodeAt(i);
}
const blob = new Blob([bytes], { type: 'audio/mp3' });
const url = URL.createObjectURL(blob);
const audioObj = new Audio(url);
audioObj.play();
If the API key is missing or the request fails, the component falls back to the browser's native SpeechSynthesisUtterance engine, keeping the app completely functional.
Feature 3: Tactical Weather & Wind Risk Simulator
API Endpoint: /api/risk-simulator
Model: gemini-3.5-flash
Structured JSON Output:
To ensure reliable metrics parsing, the prompt forces the model to return raw, validated JSON using structured schemas:
code
TypeScript
config: {
  responseMimeType: 'application/json',
}
This returns five calibrated risk percentages (Crash, Echelon, Dehydration, Descent, and Success) along with tailored analyst advice based on the active weather conditions.
CHAPTER 9: Conceptual Proposal: Three Brand-New Additional Wow AI Features
To elevate the application further, we propose three additional AI features. These expand the simulator's capabilities, introducing multi-agent simulations, sarcastic commentary, and real-time glycogen forecasting.
code
Code
+----------------------------------------------------------------------------------------+
  |                               3 NEW ADDITIONAL WOW FEATURES                            |
  +----------------------------------------------------------------------------------------+
  |  [WOW #4: Peloton Swarm Simulator]                                                     |
  |  Models 170 riders as AI agents to simulate tactical breakaways & counter-attacks.     |
  +----------------------------------------------------------------------------------------+
  |  [WOW #5: "Le Singe du Tour" Critic]                                                   |
  |  A grumpy 1970s French journalist chat bot who critiques strategies with humor.       |
  +----------------------------------------------------------------------------------------+
  |  [WOW #6: Glycogen Burn Predictor]                                                     |
  |  Simulates real-time energy burn, predicting exactly when a rider will "bonk".          |
  +----------------------------------------------------------------------------------------+
WOW FEATURE #4: AI-Driven Peloton Swarm Simulator (Dynamic Multi-Agent Swarm Intelligence)
Concept: Models the rest of the peloton (the other 170 riders) as an active multi-agent system. Instead of relying on static scripts, this feature uses Gemini to simulate how rival teams (like Visma and UAE) react to the user's tactical moves.
Architectural Flow:
The user clicks "Launch Solo Attack" at KM 150.
The client fires a request to /api/peloton-swarm containing the current race state, active wind speeds, and the user's selected tactics.
The backend instructs Gemini to act as a swarm simulator, calculating the counter-moves of key rival riders.
The API returns a structured JSON payload detailing who chases, who sits back, and how the time gap shifts over the next 10 kilometers.
Prompt Interface:
code
TypeScript
const prompt = `Act as a multi-agent cycling simulation engine. The user's rider, ${player.name}, has launched an attack on Stage ${stage.number}.
Rival Teams: Team Visma (Leader: Jonas Vingegaard), Red Bull (Leader: Primož Roglič).
Active Wind Speed: ${weatherWindSpeed} km/h. Road Slope: ${currentSlope}%.

Determine the exact response of rival agents over the next 10km.
Respond ONLY with a valid JSON array of agent responses:
[
  {
    "team": "Team Visma",
    "action": "Chase" | "Shed Domestiques" | "Counter-Attack" | "Hold",
    "wattageOutput": number,
    "tacticalReasoning": "string"
  }
]`;
WOW FEATURE #5: "Le Singe du Tour" (Sarcastic Retro-French Cycling Critic & Caricature Generator)
Concept: A parody chat assistant modeled after a fictional, grumpy 1970s French cycling journalist who critiques the user's strategy.
Architectural Flow:
The chat assistant is displayed as an old-school typewriter terminal at the bottom of the sandbox.
The user types a message or submits their strategy (e.g., "I'm going to attack early on the Tourmalet using Mads Pedersen").
The backend processes the message using Gemini, styled with a specialized system instruction.
The model responds with highly critical, sarcastic commentary, comparing modern cycling gadgets to the grit of historical racers.
System Instruction:
code
Code
You are 'Jacques "Le Singe" Lefevre', a legendary, extremely cynical, grumpy French cycling journalist who wrote for L'Équipe in the 1970s.
You despise modern cycling technology (power meters, heart rate monitors, carbon frames, tubeless tires).
You believe true racing is about grit, red wine, and raw suffering.
Critique the user's tactical choices with severe skepticism, dry wit, and retro cycling terms.
Begin responses with expressive French grunts (e.g., "Pah!", "Sacré bleu!", "Mon dieu...").
Keep your critiques short, highly entertaining, and deeply sarcastic.
WOW FEATURE #6: Biometric Aerobic Threshold & Glycogen Burn Predictor (AI-Simulated Coach)
Concept: Simulates real-time energy expenditure during the race, predicting exactly when a rider will "bonk" (deplete muscle glycogen) based on hills, heat, and effort.
Architectural Flow:
The client monitors the simulation timeline, tracking active wattage and climbing grades.
The predictor calculates glycogen burn rate based on temperature (Dehydration Index) and climbing effort.
It displays a live "Glycogen Fuel Tank" percentage alongside a "Biometric Coach Advisory" card.
The advisor uses Gemini to suggest live pacing adjustments, detailing when the rider should consume carb gels or drop back for fresh bottles.
Prompt Interface:
code
TypeScript
const prompt = `You are the chief performance coach for ${team.name}.
Rider: ${player.name} (Stamina: ${player.stats.stamina}, Climbing: ${player.stats.climbing}).
Current Segment: Stage ${stage.number}, riding at ${userWatts} watts on a ${slope}% gradient in ${weatherTemp}°C heat.
Glycogen remaining: ${userGlycogen}%.

Calculate:
1. Exact minutes until physiological glycogen depletion ("bonking").
2. Recommended shifting cadence (RPM).
3. Feeding advice (e.g., "Consume 90g glucose gel immediately").

Provide advice in a concise coach style.`;
CHAPTER 10: Bugs Triage & Code Quality Guide
An audit of the application's codebase reveals several areas where we can improve stability, optimize performance, and prevent runtime errors. Below is a detailed triage guide.
Bug 1 : Missing JSON Markdown Guards on Risk Response Parsing
Symptom: The risk simulator endpoint (/api/risk-simulator) occasionally throws JSON parsing errors, crashing the request.
Cause: Despite requesting raw JSON in the prompt, Gemini sometimes wraps its response in markdown code blocks (e.g., ```json ... ```). Calling JSON.parse() on this raw string throws a syntax error.
The Fix: Implement a sanitization utility in server.ts to strip markdown wrappers before parsing:
code
TypeScript
function sanitizeJsonResponse(rawText: string): string {
  let clean = rawText.trim();
  // Remove leading markdown code block markers
  if (clean.startsWith('```json')) {
    clean = clean.substring(7);
  } else if (clean.startsWith('```')) {
    clean = clean.substring(3);
  }
  // Remove trailing markers
  if (clean.endsWith('```')) {
    clean = clean.substring(0, clean.length - 3);
  }
  return clean.trim();
}
Update the parsing block:
code
TypeScript
const cleanJson = sanitizeJsonResponse(response.text);
const parsed = JSON.parse(cleanJson);
Bug 2 : Missing React useEffect Cleanups & Memory Leaks on Replay Tab Unmount
Symptom: The browser's CPU usage spikes after users switch tabs repeatedly during active playback.
Cause: The interval timer in LiveReplay.tsx does not clear properly if the tab is switched or the component is unmounted during playback.
The Fix: Ensure the interval is cleared immediately on unmount or when dependencies change. The existing cleanup return:
code
TypeScript
return () => clearInterval(timer);
is correct, but we must verify that no other interval-setting side effects (like those in LiveSearchStandings.tsx or audio playback loops) run without cleanup. For instance, in LiveSearchStandings.tsx:
code
TypeScript
useEffect(() => {
  fetchLiveStandings();
  const interval = setInterval(() => {
    fetchLiveStandings(true);
  }, 90000);
  return () => clearInterval(interval); // Properly clears background standing fetches
}, [selectedModel.id]);
Bug 3 : Handling Missing API Keys and Connection Failures Gracefully
Symptom: The application displays loading indicators indefinitely if the backend cannot connect to the Gemini API due to network timeouts or missing keys.
Cause: API calls in the frontend lack error fallback states, leaving components stuck in loading modes.
The Fix: Add structured try-catch-finally wrappers in the frontend components, and ensure the backend returns clear error statuses. For example, in App.tsx:
code
TypeScript
const generateRaceStory = async () => {
  setStoryLoading(true);
  try {
    const response = await fetch('/api/custom-story', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        player: selectedPlayer,
        team: selectedTeam,
        stage: selectedStage,
        model: selectedModel.id
      })
    });
    if (!response.ok) throw new Error('Network response was not ok');
    const data = await response.json();
    if (data.success) {
      setStory(data.text);
    } else {
      setStory("⚠️ Unable to compile the race story. Check your connection or API key configuration.");
    }
  } catch (error) {
    console.error('Error generating race story:', error);
    setStory("❌ Connection failed. Please check that the server is online.");
  } finally {
    setStoryLoading(false);
  }
};
Bug 4 : Handling State mismatches when selecting non-existent Replay stages
Symptom: If a user selects a stage in the selector that lacks seed replay data, the Replay tab crashes.
Cause: In LiveReplay.tsx, the component references REPLAY_SEEDS[selectedStageNum]. If selectedStageNum is not seeded, this reference is undefined, causing property lookup crashes on the next line.
The Fix: Add a fallback check in LiveReplay.tsx to handle unseeded stages gracefully:
code
TypeScript
const activeStageReplay = REPLAY_SEEDS[selectedStageNum] || REPLAY_SEEDS[9]; // Fallback to Stage 9 default
CHAPTER 11: Scalability, Security, & Deployment Architecture
To scale the Tour de France simulator for large audiences during live events in July 2026, the architecture must support rapid scaling, secure credential management, and efficient caching.
code
Code
+-----------------------------------------+
                  |           CLIENT TRAFFIC                |
                  +--------------------+--------------------+
                                       |
                                       v
                  +--------------------+--------------------+
                  |        Cloudflare CDN / Cache           |
                  |  - Caches /api/search-results (60s)     |
                  |  - Static /dist SPA Assets (Persistent)  |
                  +--------------------+--------------------+
                                       |
                                       v
                  +--------------------+--------------------+
                  |       Cloud Run Container Cluster        |
                  |  - Scale-to-zero instances (Port 3000)  |
                  |  - Node.js production container         |
                  +--------------------+--------------------+
                                       |
                        +--------------+--------------+
                        |                             |
                        v                             v
            +-----------------------+     +-----------------------+
            |  Secret Manager       |     |  Google Search        |
            |  - GEMINI_API_KEY     |     |  Grounding API        |
            +-----------------------+     +-----------------------+
1. Security & Key Management
Encapsulated Secrets: All API keys remain strictly encapsulated in the server-side environment. No variables prefixed with VITE_ are used for sensitive APIs, keeping keys hidden from client-side network inspectors.
Environment Separation: API keys are injected dynamically via platform secrets in development and deployment, keeping them out of source control.
Sanitized Inputs: All user inputs (rider selections, text prompts) are validated and sanitized on the server before being passed to Gemini, preventing prompt-injection attacks.
2. Caching Strategy for High-Traffic Events
During high-stakes stages, thousands of concurrent users may query the standings API. Making a live Gemini search call for every single user is inefficient and expensive.
Edge Caching: Configure edge caching (such as Cloudflare) on search endpoints like /api/search-results. A short cache lifetime (e.g., s-maxage=60) ensures users see fresh content while reducing API load.
Server Cache Loop: Implement an in-memory cache in server.ts to store standings for up to 90 seconds. This ensures that concurrent requests receive the cached standing data instantly, protecting the app from hitting API rate limits.
code
TypeScript
let cachedStandings: { text: string; sources: any[]; timestamp: number } | null = null;

app.post('/api/search-results', async (req, res) => {
  const now = Date.now();
  if (cachedStandings && (now - cachedStandings.timestamp < 90000)) {
    return res.json({
      success: true,
      simulated: false,
      text: cachedStandings.text,
      sources: cachedStandings.sources,
      cached: true
    });
  }
  // If cache is expired, call Gemini API and update the cache...
});
3. High-Performance Containerization
Self-Contained Bundling: The build system compiles the TypeScript server into a single, bundled CommonJS file (dist/server.cjs) using esbuild. This bypasses complex runtime imports, leading to faster startup times when the container scales up.
Scale-to-Zero Configuration: The production container is deployed on Cloud Run, which scale down to zero instances when idle to minimize costs, and spins up new instances in under two seconds to handle incoming traffic surges.
CHAPTER 12: 20 Comprehensive Technical & Tactical Follow-Up Questions
To further explore, refine, and optimize the Tour de France 2026 Simulator, we present 20 structured, professional follow-up questions. These questions are divided into software engineering and cycling sports science topics.
Category A: Full-Stack Engineering, AI Architecture, & Security
Caching and Rate-Limiting: How should we design our server-side caching layer to handle millions of concurrent hits on the /api/search-results endpoint during a live Tour mountain finish, preventing rate-limiting on our Gemini API keys?
Offline Mode Recovery: How can we improve our frontend offline fallback system to store cached search reports, custom stories, and stage profiles using browser IndexedDB, letting users run simulations when they lose connection?
Optimizing TTS Performance: Since TTS generation via the gemini-3.1-flash-tts-preview model has a slightly higher latency, how can we implement streaming audio chunking or pre-generate common commentary segments to ensure lag-free playback?
Validating JSON Structuring: What strategies can we use to guarantee 100% schema compliance for Gemini’s JSON risk responses, preventing app crashes if the model returns missing properties or invalid structures?
Protecting Against Prompt Injection: How can we protect our text inputs (like the TTS commentary custom text field) against malicious prompt injection attacks that try to bypass system instructions or extract secrets?
Integrating Google Maps Platform: How can we replace our custom vector SVG topographical traces in RouteViewer.tsx with real Google Maps data, overlaying exact route coordinates and terrain gradients using the Places and Routes APIs?
Database Storage and Auth: If the simulator expands to let users save custom campaigns, how should we structure our Firestore database rules to isolate user data while supporting real-time collaborative strategy boards?
Automated Testing Suite: How can we build an automated test suite (using tools like Playwright or Vitest) to verify that our Express API endpoints, static file serving, and React layout animations perform correctly across different screen resolutions?
Monitoring Token Budgets: Since generating rich, 4-part sports broadcast stories can consume significant tokens, how can we monitor and optimize our prompt tokens to balance narrative detail with cost-efficiency?
Minimizing Cold Start Delays: What optimizations can we apply to our esbuild configurations to keep our compiled dist/server.cjs bundle as small as possible, minimizing container cold start delays on Google Cloud Run?
Category B: Sports Science, Aerodynamics, & Grand Tour Tactics
Simulating Drafting Drag Friction: How can we model the physical effects of drafting? For instance, how do we calculate the exact aerodynamic drag reduction (e.g., 30-40% energy saved) when a leader sits behind three team domestiques?
Modifying Watts-per-Kilogram for Climbs: How does a climber's power-to-weight ratio (W/kg) change on steep slopes over 10%? How should our simulator adjust climbing performance based on a rider's physical weight?
Calculating Oxygen Depletion: At high elevations like the Col du Tourmalet (2,115m), oxygen density drops by about 20%. How can we model this aerobic performance drop within our Glycogen Burn Predictor?
Evaluating Echelon Wind Risks: How can we calculate the risk of crosswinds splitting the peloton? What formulas should we use to model wind speeds, angles, and team positioning on coastal routes like Stage 4 to Narbonne?
Predicting Descending Speeds: What physical factors (like rider weight, tire grip, wet tarmac, and gravity) should we use to simulate safe descending speeds on dangerous mountain curves?
Managing Glycogen Consumption: How can we model muscle glycogen depletion on extreme mountain stages? How do we calculate energy burn rates when riders sustain efforts above their anaerobic thresholds?
Comparing Compact Gearing Performance: How do compact gear ratios (e.g., 50/34 chainsets with 11-34T cassettes) help riders maintain healthy climbing cadences on steep grades, preventing early muscle fatigue?
Modeling the Accordion Effect: How can we simulate the "accordion effect" (frequent braking and accelerating around tight hairpin corners)? Why does staying near the front of the peloton protect leaders from crashes?
Calibrating Wet-Road Tire Grip: How much does rainy weather reduce cornering traction on descents? How do team mechanics adjust tire pressures (e.g., dropping 0.4 to 0.8 bar) to maximize grip on wet tarmac?
Calculating Time Cut Percentages: How do race organizers calculate time cuts for flat versus extreme mountain stages? How can we model the gruppetto's pacing strategy to ensure sprinters finish within the official time limit?
CHAPTER 13: Technical Spec Summary & Professional Overview
This technical specification details the complete, robust architecture of the Tour de France 2026 Live Simulator & Interactive Storyboard.
Unified Sandbox State: Designed a clean, server-side architecture that encapsulates all third-party API configurations while serving responsive, fast interactive states to the client.
Real-Time Integration: Outlined a search-grounded state-recovery loop that connects the Gemini API to live web searches, bringing real-world 2026 standings and results directly into the UI.
Immersive Experiences: Detailed a state-driven replay timeline and a multi-view spatial visualizer, giving users a complete, tactical look at the race from every angle.
Original AI Features: Described the integration of three core AI features: Team Car Strategy Briefings, Live Radio Tour Audio Broadcasters (TTS), and Weather Risk Scorecards.
Conceptual Upgrades: Proposed three brand-new AI integrations: Peloton Swarm Simulators (multi-agent modeling), Retro-French Critic Chat Assistants, and Biometric Glycogen Predictors.
Production-Ready Engineering: Audited the codebase to provide concrete bug triages, caching strategies for high-traffic events, and containerized deployment blueprints on Google Cloud Run.
Comprehensive Exploration: Ended with 20 structured, professional follow-up questions to guide the future development, optimization, and scientific accuracy of the simulator.
