# 醫療器材追蹤與管理系統（數位雙生模擬應用）

## 技術規格書與系統架構藍圖 (Technical Specification & Architectural Blueprint)

---

## 1. 執行摘要與設計哲學 (Executive Summary & Design Philosophy)

### 1.1 系統概述 (System Overview)

本系統是一款全棧、單頁面的網頁應用程式（Single-Page Web Application），旨在將前作「植物精靈冒險 JRPG」的遊戲化機制，全面重構並轉型為專為醫療機構設計的「醫療器材追蹤與管理系統（Medical Device Tracking System）」。本系統融合了醫院日常臨床審計、物流調度與生物醫學工程（Biomedical Engineering）的業務邏輯，以數位雙生（Digital Twin）與遊戲化（Gamification）的形式，提供高可視化、具備預測性維護能力的醫材資產管理平台。

系統核心將原本的「走路種花、精靈小隊、探索打怪、鍊金合成」機制，精準映射至「院區資產巡檢、醫材群組管理、硬體除錯與網絡防禦、軟體定義升級與耗材配置」。前端利用高保真度（High-Fidelity）的 2D HTML5 Canvas 渲染引擎，將複雜的醫院資產數據與流轉狀態解構成五大電影級視覺管道（Cinematic View Pipelines）：

* **科室走廊視差視圖 (Ward Corridor Parallax View)**：側視角的多層次動態景觀，以流體粒子特效模擬醫材在各科室間的傳送與訊號同步軌跡。
* **技術除錯網格視圖 (Tactical Debug Grid View)**：戰術網格布局，將醫療器材的硬體校正、韌體除錯與網絡安全防禦轉化為回合制 JRPG 戰術決策面板。
* **第一人稱巡檢視圖 (First-Person Inspection View)**：偽 3D 透視投影視覺，模擬醫工人員或自動導引車（AGV）穿梭於醫院長廊，進行實時資產節點掃描。
* **等角醫工整備中心視圖 (Isometric Bio-Med Hub View)**：三維等角投影（Isometric）的中心SAN倉庫與中央消毒供應中心（CSSD）視覺，展示資產充電、報廢檢驗與待料整備狀態。
* **全域資產拓撲雷達視圖 (Overworld Asset Topology Radar View)**：深色調的室內實時定位系統（RTLS）與跨院區物流供應鏈雷達圖，追蹤 GPS/BLE 藍牙同步線與資產告警標記。

後端由 Node.js、Express 與 Vite 驅動，並全面整合 Google Gemini AI 引擎（使用 `@google/genai` TypeScript SDK）。AI 在本系統中扮演核心的「資產調度大師」與「隨機事故生成器」，負責動態生成高度結構化的醫院模擬拓撲地圖、派發符合醫療法規（如 FDA/ISO）的臨床工單任務，並對醫材的運行的維護日誌進行深度微調與預測性維護分析。

```
+---------------------------------------------------------------------------------+
|                                  USER BROWSER                                   |
|                                                                                 |
|  +-------------------+  +------------------+  +-------------------+             |
|  |    GameCanvas     |  |    MapEditor     |  | AssetManagement   |             |
|  | (5 Cinematic Views)|  | (Manual & AI UI) |  | (Roster & Modules)|             |
|  +---------+---------+  +--------+---------+  +---------+---------+             |
|            |                     |                      |                       |
|            +---------------------+----------------------+                       |
|                                  | (Client-Server APIs)                         |
|                                  v                                              |
+----------------------------------+----------------------------------------------+
                                   |
                                   v
+----------------------------------+----------------------------------------------+
|                           EXPRESS BACKEND (PORT 3000)                           |
|                                                                                 |
|  +---------------------------------------------------------------------------+  |
|  |                          Vite Middleware (Dev Mode)                        |  |
|  +---------------------------------------------------------------------------+  |
|  |                            API Routing Layer                              |  |
|  |  - /api/gemini/generate-map     - /api/gemini/generate-quest              |  |
|  |  - /api/gemini/refine-character - /api/gemini/mutate-seedling             |  |
|  |  - /api/gemini/expedition-podcast - /api/gemini/generate-soundtrack       |  |
|  +------------------------------------+--------------------------------------+  |
|                                       | (Secure API Key Wrapper)                |
|                                       v                                         |
|                      +----------------+---------------+                         |
|                      |        @google/genai SDK       |                         |
|                      +----------------+---------------+                         |
|                                       |                                         |
+---------------------------------------|-----------------------------------------+
                                        v
                            =========================
                             GOOGLE GEMINI AI ENGINE
                            =========================

```

### 1.2 設計語言與視覺主題 (Design Language & Visual Themes)

為了擺脫傳統醫療資產管理系統（EAM/CMMS）死板、冰冷且充斥著密集表格的網頁布局，本系統採用「科技醫療風（Clinical Cyberpunk Theme）」的暗色畫布面板，在保護長時間監控螢幕的醫工人員視力的同時，利用高對比度的霓虹與臨床警報色凸顯關鍵資產數據。

#### 色彩矩陣 (Color Palette)

* **主畫布背景色 (Primary Canvas Background)**：採用深邃臨床藍（Deep Clinical Navy, `#0a0e1a` 至 `#161f38`），模擬封閉式中央控制室的夜間監配氛圍，讓發光的 Canvas 資產物件具備極高的動態範圍。
* **正常運作指標 (Operational Green)**：翡翠綠（Emerald Health, `#10ac84` / `#2ecc71`），用於表示無線訊號滿格、資產合規率達標、電力充足及處於正常工作狀態的設備。
* **警告與待校正指標 (Maintenance Yellow)**：琥珀金（Maintenance Amber, `#f1c40f` / `#fed330`），用於提示需要進行年度校正、耗材即將耗盡、或收到非緊急工單的醫材。
* **緊急與防禦告警指標 (Clinical Alarm Red)**：警報紅（Alarm Pink-Red, `#ff4757` / `#fd79a8`），用於標示資產離線、電池嚴重衰減、遭受惡意網絡攻擊、或是面臨臨床緊急調度的區域。

#### 字體配置 (Typography Pairings)

* **顯示層標題 (Display Headings - H1, H2, 卡片標頭)**：選用 `Space Grotesk`。這款幾何型、高對比的結構化無襯線字體，能夠賦予醫材系統強烈的工業與科技幾何感。
* **副標題與環境描述 (Subheadings & Ambient Logistics Text)**：引進 `Playfair Display` 襯線字體，在高度冰冷的技術數據中注入一絲人文故事感，用於顯示 AI 生成的設備維修履歷與臨床情境描述。
* **數據計數器、坐標與日誌流 (Data Counters, Stats, & Telemetry Logs)**：嚴格採用 `JetBrains Mono` 等寬字體，確保所有的設備 UUID、室內定位坐標、合規百分比及後端 API 響應時間的排版絕對對齊，提升可讀性。

---

## 2. 主系統架構與檔案拓撲 (Master System Architecture & File Topology)

本系統以全棧 TypeScript 架構運行，容器化後部署於 Google Cloud Run。前端採用 React + Vite 進行高度解耦的單頁面渲染，後端 Express 則專注於處理與 Gemini AI 引擎的通訊、資料加密與防呆驗證，確保客戶端的動畫渲染不會受到伺服器端重度 AI 推理的阻塞。

### 2.1 檔案系統分解 (File System Breakdown)

```
.
├── .env.example
├── .gitignore
├── index.html
├── metadata.json
├── package.json
├── server.ts
├── tsconfig.json
├── vite.config.ts
├── assets
│   └── .aistudio
│       └── .gitignore
└── src
    ├── App.tsx
    ├── index.css
    ├── main.tsx
    ├── types.ts
    └── components
        ├── AIControlPanel.tsx
        ├── AssetManagement.tsx
        ├── GameCanvas.tsx
        └── MapEditor.tsx

```

* **`/server.ts`**：全系統的統一 Node.js 入口點。在開發模式下掛載 Vite 中間件以提供熱插拔（HMR），在生產模式下直接將 `/dist` 目錄作為靜態資源託管，並暴露出六大專屬的 Gemini AI JSON 路由端點。
* **`/src/types.ts`**：全域型別與枚舉的唯一真相來源（Single Source of Truth）。定義了醫材資產、擴充組件、院區地圖拓撲、即時科室狀態、環境負載以及全局追蹤指標的嚴格 TypeScript Interface。
* **`/src/App.tsx`**：全局狀態調度器。維護全院資產的狀態同步，驅動核心計時器（Simulation Ticks），模擬醫工巡檢步數、RFID 數據吞吐量，並協調各個側邊欄組件的資料分發。
* **`/src/components/GameCanvas.tsx`**：高生能 HTML5 Canvas 視圖調度中心。封裝了五個獨立的渲染管道，並在網格視圖中處理基於狀態機的醫材除錯與防禦計算。
* **`/src/components/MapEditor.tsx`**：雙模態院區地圖設計器。支援手動點位配置與基於 AI 自然語言提示詞的科室地圖生成。
* **`/src/components/AssetManagement.tsx`**：醫材履歷工作表。展示 AI 生成的設備背景故事，管理擴充組件的熱插拔升級，並追蹤設備運行小時數。
* **`/src/components/AIControlPanel.tsx`**：控制抽屜。內置 Gemini 模型切換開關、即時延遲（Latency）監控圖表以及系統 Prompt 微調參數。

### 2.2 編譯、打包與伺服器運行時 (Compilation, Bundling, & Server Runtime)

為確保系統具備極高的冷啟動速度，且徹底免除 Node.js 在原生 ES Modules 下對相對路徑引入的嚴格檢查，本系統在生產環境採用雙通道打包策略：

1. **Vite Build**：編譯 React 客戶端代碼，進行 Tailwind CSS 的 Tree-shaking 壓縮，將單頁面資源打包輸出至 `/dist`。
2. **esbuild Server Bundler**：將 `/server.ts` 連同其依賴項（排除 `@google/genai` 等外部原生模組）打包成單一的 CommonJS 目標檔案 (`/dist/server.cjs`)。

```json
{
  "scripts": {
    "dev": "tsx server.ts",
    "build": "vite build && esbuild server.ts --bundle --platform=node --format=cjs --packages=external --sourcemap --outfile=dist/server.cjs",
    "start": "node dist/server.cjs"
  }
}

```

---

## 3. 領域模型與全域型別系統 (Domain Models & Type System)

所有核心實體與商業邏輯模型均嚴格定義於 `src/types.ts`，以確保前端模擬渲染與後端 AI 生成的 JSON Schema 達到像素級別的型別對齊。

### 3.1 核心枚舉與介面定義

```typescript
// 醫院運作負載狀態（映射原天氣系統）
export enum HospitalLoadStatus {
  NORMAL = "Normal",       // 常態運作：基礎渲染覆蓋率
  SURGE = "Surge",         // 急診湧入：警報粒子倍增，負載係數高
  NIGHT_SHIFT = "Night",   // 夜間低負載：光效變暗，著重於耗電控制
  OUTAGE = "Outage"        // 網路/電力中斷：霓虹閃爍，進入硬體應變模式
}

// 作用中的視圖管道
export enum RouteViewType {
  WARD_CORRIDOR = "WardCorridor",
  TACTICAL_DEBUG = "TacticalDebug",
  FIRST_PERSON_INSPECT = "FirstPersonInspect",
  ISOMETRIC_BIOMED_HUB = "IsometricBioMedHub",
  OVERWORLD_RADAR = "OverworldRadar"
}

// 醫材擴充模組/零組件（映射原裝備系統）
export interface AssetModule {
  id: string;
  name: string;
  type: "hardware" | "firmware" | "accessory";
  description: string;
  statBonus: {
    signalStrength?: number;    // 提升訊號覆蓋半徑
    calibrationAccuracy?: number; // 提升除錯成功率
    powerEfficiency?: number;    // 降低耗電速率
  };
  rarity: "Standard" | "Advanced" | "Critical" | "Enterprise";
}

// 醫療器材資產實體（映射原角色系統）
export interface MedicalAsset {
  id: string;
  name: string; // 例如: "Infusion Pump Beta-9"
  deviceClass: string; // 例如: "Class II Variable Pump"
  operationalHours: number;
  maintenanceCycleMonths: number;
  batteryHealth: number; // 0-100% (原 HP)
  maxBattery: number;
  stats: {
    debugPower: number;      // 韌體修復基本能力 (原 Atk)
    cyberDefense: number;    // 網絡安全防禦牆 (原 Def)
    syncSpeed: number;       // 資料同步速率 (原 Speed)
    coverageRate: number;    // 訊號廣播效率 (原 PlantRate)
  };
  hexColor: string; // 代表該醫材的分類霓虹色彩
  assetType: "Pump" | "Defibrillator" | "Ventilator" | "Imaging" | "Monitor";
  modules: {
    hardware?: AssetModule;
    firmware?: AssetModule;
    accessory?: AssetModule;
  };
  maintenanceLogs: string; // AI 生成的維修履歷與背景故事
}

// 無線信號同步基站（映射原花卉節點）
export interface SyncNode {
  id: string;
  name: string;
  color: string;
  x: number; // 歸一化坐標 (0-100%)
  y: number;
  radius: number; // 覆蓋範圍直徑
}

// 系統故障與漏洞目標（映射原敌對動物）
export interface SystemVulnerability {
  id: string;
  name: string;
  type: "Error_404" | "Malware_Leak" | "Battery_Degradation" | "Bio_Contamination";
  behavior: "benign" | "neutral" | "critical"; // 嚴重程度
  color: string;
  x: number;
  y: number;
  threatLevel: number; // 戰術網格中的血量 (原 HP)
  maxThreat: number;
}

// 掉落物資與備品（映射原寶箱）
export interface SparePart {
  id: string;
  name: string;
  type: "battery" | "sensor" | "sterilizer" | "tool_kit";
  color: string;
  x: number;
  y: number;
  isCollected: boolean;
}

// 資產巡檢路線
export interface InspectionRoute {
  id: string;
  name: string;
  description: string;
  riskLevel: "Low" | "Medium" | "High" | "Biohazard";
  checkpoints: Array<{ name: string; x: number; y: number }>;
  syncNodes: SyncNode[];
  vulnerabilities: SystemVulnerability[];
  spareParts: SparePart[];
  lengthMeters: number;
}

// 醫院數位雙生全域狀態
export interface HospitalGameState {
  overallCompliance: number;       // 全院資產合規率 (原 Score)
  bandwidthReserve: number;        // 無線網卡剩餘頻寬 (原 Petals)
  totalDistanceAudited: number;    // 累計巡檢覆蓋長度
  activeMapId: string;
  activeRouteId: string;
  activeView: RouteViewType;
  managedAssets: MedicalAsset[];
  inventoryModules: AssetModule[];
  currentLoad: HospitalLoadStatus;
  inspectionSpeedMultiplier: number; // 模擬巡檢速率 (1-5x)
  isTransmittingTelemetry: boolean;  // 是否正在實時廣播 (原 isPlanting)
  activeOrder?: {
    id: string;
    description: string;
    targetProgress: number;
    currentProgress: number;
    complianceReward: string;
  };
}

```

---

## 4. 前端渲染管道與 Canvas 引擎重構 (Frontend Rendering Pipelines)

`GameCanvas.tsx` 是系統的圖形中樞，捨棄了高開銷的 DOM 節點，完全在一塊 HTML5 Canvas 上利用一個精密的 `requestAnimationFrame` 迴圈驅動五大視圖的動態切換。

```
+-------------------------------------------------------------------------+
|                              GameCanvas.tsx                             |
|                                                                         |
|      +-----------------------------------------------------------+      |
|      |               Unified requestAnimationFrame               |      |
|      +-----------------------------+-----------------------------+      |
|                                    |                                    |
|      +-----------------------------+-----------------------------+      |
|      |                 RouteViewType Dispatcher                  |      |
|      +---+-------------+-------------+-------------+-------------+      |
|          |             |             |             |             |      |
|          v             v             v             v             v      |
|     +---------+   +---------+   +---------+   +---------+   +---------+ |
|     |  Ward   |   |Tactical |   |  First  |   |Isometric|   |Overworld| |
|     |Corridor |   | Debug   |   | Person  |   | Bio-Med |   | Topology| |
|     |Parallax |   |  Grid   |   | Inspect |   |   Hub   |   |  Radar  | |
|     +---------+   +---------+   +---------+   +---------+   +---------+ |
+-------------------------------------------------------------------------+

```

### 4.1 管道 1：科室走廊視差視圖 (Ward Corridor Parallax View)

該管道水平模擬資產在不同醫療科室間的流轉。視差滾動效果（Parallax Scrolling）將整塊醫院景深切分為四層：

* **第 1 層（背景天際線/全院鳥瞰）**：依據 `HospitalLoadStatus` 渲染漸變色。常態運作（NORMAL）時呈現溫和的科技藍綠漸變（`#0a1128` 到 `#001f3f`）；當處於急診湧入（SURGE）狀態時，漸變色會與暗紅色（`#2c000e`）融合，並以餘弦函數繪製呼吸式的淡紅色動態光暈。
* **第 2 層（遠景科室線）**：以低對比度線條繪製遠處的手術室、放射科廊道剪影，以巡檢移動速度的 10% 進行極慢的水平位移。
* **第 3 層（中景護理站）**：繪製藥櫃、中央電腦螢幕牆與護理站標記，以 25% 的速度滾動。
* **第 4 層（前景通道與傳送帶）**：靜止的水平基準線（`#1c2642`），中央繪製一條發光的數據匯流排軌道。

醫材資產以小隊隊伍形式排開，其水平坐標添加固定的偏移阻尼。為了模擬運作狀態，每個醫材元件的 Y 軸位置均被注入一個微幅的縱向波動函數：

$$\text{offset}_Y = \sin(\text{tick} \times 0.08 + \text{index}) \times 4.5$$

當全域的 `isTransmittingTelemetry`（實時廣播）開啟時，醫材背後會以粒子系統不斷釋放出符合其專屬 `hexColor` 的圓形數據包粒子。這些粒子隨著時間推移其半徑縮小、透明度依據指數衰減，並向後飄散。

### 4.2 管道 2：技術除錯網格視圖 (Tactical Debug Grid View)

此視圖將畫布重新轉化為一個嚴謹的回合制戰術網格面板，背景繪製間距為 40 像素的暗色調矩陣網格線（`#12192c`）。

* **我方資產配置（左側）**：醫材設備縱向排列於網格的左三分之一處（$X_{\text{base}} = 120$）。每個醫材下方繪製一個發光的發光二極體橢圓底座。如果設備安裝了擴充模組，則在底座周邊繪製附加的霓虹小方塊（例如：紅色代表硬體升級、白色代表防禦性韌體）。
* **故障與漏洞威脅（右側）**：系統故障碼或網絡威脅排列於右三分之一處（$X_{\text{target}} = \text{canvas.width} - 160$）。當前被選取的威脅目標周圍會被四個直角定位錨點鎖定，動態計算旋轉角度。
* **核心運算機制**：
* **韌體修復 (Debug Power - 原攻擊)**：隨機加入方差係數，計算對漏洞的威脅消減量：


$$\text{Damage} = (\text{Asset Atk} + \text{Module Bonus}) \times \text{Random}(0.9, 1.15)$$


* **網絡防禦牆 (Cyber Defense - 原防禦)**：提高資產的防禦壁障，生成一面帶有淡藍色半透明外框的保護盾，扣減漏洞的反噬傷害。
* **頻寬補充 (Telemetry Reset - 原補血)**：消耗 `bandwidthReserve`，全局修復全隊醫材的 `batteryHealth`。



### 4.3 管道 3：第一人稱巡檢視圖 (First-Person Inspection View)

該管道模擬真實的長廊透視投影，賦予使用者如同身臨其境的醫院巡檢體驗。

* **透視矩陣模擬**：利用梯形投影將中心路徑收斂至遙遠的視平線中心點（$X_{\text{center}} = \text{width}/2$, $Y_{\text{horizon}} = \text{height}/3$）。
* **資產節點的平方級放大**：長廊兩側的無線信號基站（`SyncNode`）或散落的備品，其畫面縮放比例（Scale）與偏離中心的距離，隨著巡檢進度呈二次方曲線（Quadratic Projection Curve）快速逼近：

$$\text{Scale} = \left(\frac{\text{progress}_{\text{entity}}}{\text{max\_depth}}\right)^2, \quad X_{\text{render}} = X_{\text{center}} + (X_{\text{entity}} - X_{\text{center}}) \times \text{Scale}$$

* **巡檢員視角 bobbing**：底部的我方資產模型僅露出上半部分，並隨著走動步伐進行左右交流式擺動，擺動幅度與巡檢速率成正比。

### 4.4 管道 4：等角醫工整備中心視圖 (Isometric Bio-Med Hub View)

這是一個全三維立體感的靜態整備畫面，將 2D 地圖網格坐標 $(X, Y)$ 經由等角投影轉換矩陣對齊至 3D 空間：

$$X_{\text{iso}} = (X - Y) \times \cos(30^\circ) + X_{\text{offset}}$$

$$Y_{\text{iso}} = (X + Y) \times \sin(30^\circ) + Y_{\text{offset}}$$

* **建築物結構繪製**：中央消毒供應中心（CSSD）、高壓充電樁、待料庫房等建築均由三個互相關聯的多邊形（頂部菱形、左側牆面、右側牆面）拼接而成。透過控制三面多邊形的明暗色彩差，營造出立體的光影方塊感。
* **資產隨機游走行為**：未出勤的醫材在整備中心的空地上散步，其游走偏移量由極低頻率的三角函數混合驅動，以確保動作流暢自然：

$$\Delta X = \cos(\text{tick} \times 0.02) \times 1.2, \quad \Delta Y = \sin(\text{tick} \times 0.015) \times 0.9$$

### 4.5 管道 5：全域資產拓撲雷達視圖 (Overworld Asset Topology Radar View)

該視圖呈現高度抽象化的室內電子定位全景地圖。

* **拓撲等高線**：底層利用正弦波與時間變數動態擴散出一圈圈綠色或藍色的向外輻射圈，模擬院區內的無線電波干擾網格。
* **二次貝氏曲線路徑**：巡檢路線被繪製成一條光滑的動態連續貝氏曲線（Bezier Curve）。當 `isTransmittingTelemetry` 開啟時，這條線路會被覆蓋上一層粗度為 3 像素、帶有高斯模糊外發光特效的紅色光柵線。
* **雷達脈衝**：當前隊伍在路徑上的位置呈現為一個高亮點，其外圍包裹著一個半徑在 0 到 45 像素之間不斷循環縮放的淡藍色雷達探測圈。

---

## 5. 後端 API 與 Google Gemini AI 核心整合 (Backend APIs & Gemini AI Orchestration)

後端 Express 應用程序深度依賴官方推薦的最新 `@google/genai` TypeScript SDK，所有與大語言模型的對話均經過嚴格的結構化輸出（Structured Outputs）配置。

```
+---------------------------------------------------------------------------------+
|                                 EXPRESS BACKEND                                 |
|                                                                                 |
|  POST /api/gemini/generate-map                                                  |
|  +---------------------------------------------------------------------------+  |
|  |  1. 解析提示詞與科室類型 (如：ICU、重症監護)。                              |  |
|  |  2. 當 API 金鑰失效時，自動切換至本地靜態醫療拓撲 JSON 應變機制。            |  |
|  |  3. 定義 Type.OBJECT 系統架構定義，將 Temperature 調低至 0.35 確保結構穩定。 |  |
|  |  4. 呼叫 ai.models.generateContent('gemini-3.1-flash-lite')。              |  |
|  |  5. 驗證坐標範圍 (0-100%)，為生成的漏洞與組件附加唯一 UUID。                 |  |
|  |  6. 將完全型別合規的 JSON 載荷發送至客戶端網頁。                             |  |
|  +---------------------------------------------------------------------------+  |
|                                                                                 |
|  POST /api/gemini/generate-quest                                                 |
|  +---------------------------------------------------------------------------+  |
|  |  1. 接收當前路線風險係數與運作負載狀態。                                   |  |
|  |  2. 建立 Dungeon Master 系統指令，要求輸出符合 ISO 13485 流程的模擬工單。  |  |
|  |  3. 呼交大模型，將回應精準映射為 type-safe 的醫療工單任務。                  |  |
|  +---------------------------------------------------------------------------+  |
|                                                                                 |
|  POST /api/gemini/refine-character                                              |
|  +---------------------------------------------------------------------------+  |
|  |  1. 接收單台醫材的歷史維修字串與新加裝的擴充組件規格。                       |  |
|  |  2. 利用模型推理預測設備老化趨勢，計算微調後的 Atk/Def/Speed 乘數。          |  |
|  |  3. 回傳整合後的設備專屬背景履歷與狀態加成 JSON。                            |  |
|  +---------------------------------------------------------------------------+  |
+---------------------------------------------------------------------------------+

```

### 5.1 初始化伺服器端 SDK

伺服器啟動時，會從環境變數中抽離出 `GEMINI_API_KEY`。在建立 `GoogleGenAI` 實例時，透過傳入特定的系統標頭以確保平台遙測與分析的精準性：

```typescript
import { GoogleGenAI } from "@google/genai";

const apiKey = process.env.GEMINI_API_KEY;
let ai: GoogleGenAI | null = null;

if (apiKey && apiKey !== "MY_GEMINI_API_KEY") {
  try {
    ai = new GoogleGenAI({
      apiKey: apiKey,
      httpOptions: {
        headers: {
          'User-Agent': 'aistudio-build',
        }
      }
    });
    console.log("[HEALOCK] Gemini SDK 初始化成功。");
  } catch (err) {
    console.error("[HEALOCK] Gemini SDK 初始化失敗:", err);
  }
}

```

### 5.2 院區虛擬拓撲生成端點：`/api/gemini/generate-map`

* **HTTP 方法**：`POST`
* **傳入參數 Schema**：

```json
{
  "prompt": "第三智慧醫療大樓高風險ICU病房區域",
  "riskLevel": "High",
  "model": "gemini-3.1-flash-lite"
}

```

* **系統提示詞與架構限制**：
為了確保生成的地圖完全符合系統渲染規範，我們將 `responseMimeType` 設為 `"application/json"`。為了防止大模型在面對醫療場景時產生天馬行空的幻覺，將 `temperature` 收斂至 `0.35`。
系統指令：「你是一位資深的醫療機構數位雙生架構師。你必須根據給定的科室提示詞與風險等級，生成一個技術細緻、結構完全合規的醫院資產分布拓撲圖。所有的坐標 $X, Y$ 必須在 0 到 100% 的浮點數範圍內。回傳的資料必須精準符合規定的 JSON 語法，不得包含任何 Markdown 區塊或外部包裝文字。」
* **預期回傳 JSON 結構**：

```json
{
  "name": "ICU 重症智慧網絡區域",
  "description": "包含 24 床高度依賴型呼吸機組與中央監控站的敏感拓撲網路。",
  "riskLevel": "High",
  "routes": [
    {
      "name": "A廊道 呼吸機巡檢線",
      "description": "針對 1-12 床邁瑞呼吸機群的例行無線藍牙訊號同步線路。",
      "checkpoints": [
        {"name": "護理站起點", "x": 5.0, "y": 20.0},
        {"name": "1-6床隔離病室", "x": 45.5, "y": 22.0},
        {"name": "中央設備充電機櫃", "x": 95.0, "y": 18.5}
      ],
      "syncNodes": [
        {"name": "藍牙定位基站 ICU-01", "color": "#10ac84", "x": 30.0, "y": 15.0, "radius": 25}
      ],
      "vulnerabilities": [
        {"name": "輸液幫浦韌體簽章過期漏洞", "type": "Malware_Leak", "behavior": "critical", "color": "#ff4757", "x": 50.0, "y": 40.0, "threatLevel": 120, "maxThreat": 120}
      ],
      "spareParts": [
        {"name": "備用醫用級鋰電池組", "type": "battery", "color": "#f1c40f", "x": 75.0, "y": 10.0, "isCollected": false}
      ],
      "lengthMeters": 850
    }
  ]
}

```

### 5.3 臨床模擬工單生成端點：`/api/gemini/generate-quest`

* **HTTP 方法**：`POST`
* **傳入參數 Schema**：

```json
{
  "routeName": "A廊道 呼吸機巡檢線",
  "riskLevel": "High",
  "currentLoad": "Surge",
  "model": "gemini-3.1-flash-lite"
}

```

* **系統執行邏輯**：
`temperature` 設為 `0.5`。系統指令要求模型模擬一位醫院聯邦審計官，根據目前急診湧入（Surge）的極端負載狀況，為巡檢小隊定制符合醫療合規要求的突發任務（工單）。生成的 `targetProgress` 直接對齊巡檢步數或遙測資料包的傳輸吞吐量。
* **回傳 JSON 結構**：

```json
{
  "id": "task-audit-2026-xyz",
  "description": "【緊急突發工單】急診湧入導致高頻干擾，請立即開啟實時遙測廣播並巡檢完成 1500 米的資料同步，以確保 ICU 設備合規率維持在 98% 以上。",
  "targetProgress": 1500,
  "currentProgress": 0,
  "complianceReward": "Enterprise 級防輻射隔離外殼組件"
}

```

### 5.4 醫材維修履歷與效能微調端點：`/api/gemini/refine-character`

* **HTTP 方法**：`POST`
* **傳入參數 Schema**：

```json
{
  "asset": {
    "name": "Infusion Pump Beta-9",
    "deviceClass": "Class II",
    "batteryHealth": 88,
    "stats": {"debugPower": 30, "cyberDefense": 20, "syncSpeed": 15, "coverageRate": 1.2}
  },
  "customPrompt": "該設備已連續運行 400 小時，且剛剛加裝了最新的高增益無線晶片模組",
  "model": "gemini-3.1-flash-lite"
}

```

* **系統執行邏輯**：
大模型扮演資產性能預測器，解構設備當前運行的磨損狀況與硬體變動。它將輸出一份詳盡的專業維修日誌，並給出微調後的基礎屬性乘數（Multipliers），直接作用於前端的除錯戰鬥運算。
* **回傳 JSON 結構**：

```json
{
  "maintenanceLogs": "該設備在高溫高壓環境下連續運轉，電池健康度出現輕微不可逆衰減；但得益於高增益無線晶片的熱插拔升級，其訊號廣播效率獲得顯著提升，建議在下個維護週期前重點防範網絡端口暴露。",
  "statusEffect": "無線覆蓋增強狀態 (Telemetry Boosted)",
  "refinedModule": {
    "name": "升級版高增益千兆網卡",
    "description": "原廠組件封裝，大幅優化了封包丟失率。",
    "statBonus": { "signalStrength": 25, "powerEfficiency": -5 }
  },
  "statsMultiplier": {
    "debugPower": 1.0,
    "cyberDefense": 0.95,
    "syncSpeed": 1.35,
    "coverageRate": 1.75
  }
}

```

---

## 6. 三大全新 WOW AI 進階智慧功能 (3 Additional WOW AI Features)

為了進一步提升產品的技術壁壘，系統深度結合了 Gemini 3.5 的多模態推理、音訊生成與 Lyria 語音合成模型，推出了三大徹底打破傳統網頁體驗的 AI 功能模組。

```
===================================================================================
                       ADDITIONAL WOW AI ARCHITECTURES
===================================================================================

[FEATURE A: AI 醫材智慧改裝組態擴充器]
 工程師自由描述改裝配方 (語音/文字)
      |
      v
 Express POST API -------> Gemini 3.5 Flash ------> 生成改裝後設備配置 Schema
                            (Type.OBJECT)             (全新外觀顏色、防禦技能、專屬乘數)
-----------------------------------------------------------------------------------

[FEATURE B: AI 多人對話式「生物醫學工單交班廣播」TTS 系統]
 全院即時資產遙測歷史日誌
      |
      v
 Express POST API -------> Gemini 3.5 Flash -------> 生成醫工主管與護理長交班劇本
                                                              |
                                                              v
                                                   Gemini 3.1 Flash TTS
                                                              |
                                                              v
 瀏覽器 JRPG 廣播收音機組件 <------------------------- 串流 Base64 音訊二進位 Blob (WAV)
-----------------------------------------------------------------------------------

[FEATURE C: AI 臨床環境壓力自適應音效合成器]
 醫院運作負載與突發告警狀態
      |
      v
 Express POST API -------> Gemini Lyria 引擎 -------> 生成 30 秒符合 IEC 60601 規範的
                                                      臨床環境白噪音與防疲勞警告迴圈

```

### 6.1 AI 功能 A：AI 醫材智慧改裝組態擴充器 (AI Device Component Mutator)

傳統系統中，設備升級僅限於點擊固定的選單。本功能允許生物醫學工程師利用自由文字或語音輸入任意的「改裝構想」（例如：「我把這台老舊電擊器拆開，用隔離室撿到的防磁貼片包裹了變壓器，並將固件代碼刷入了開源的防網絡攔截補丁」）。

* **後端路由**：`POST /api/gemini/mutate-asset`
* **核心模型**：`gemini-3.5-flash`
* **系統架構約束**：後端嚴格注入 `responseSchema`：

```json
{
  "mutatedName": { "type": "STRING" },
  "neonVisualColor": { "type": "STRING", "description": "符合改裝氣質的霓虹十六進位顏色碼" },
  "technicalDescriptor": { "type": "STRING", "description": "高度專業的改裝原理解釋與預後維護評估" },
  "unlockedSkill": { "type": "STRING", "description": "在除錯網格視圖中解鎖的專屬防禦或攻擊招式名稱" },
  "calculatedModifiers": {
    "type": "OBJECT",
    "properties": {
      "targetStat": { "type": "STRING", "description": "debugPower | cyberDefense | syncSpeed | coverageRate" },
      "multiplier": { "type": "NUMBER" }
    },
    "required": ["targetStat", "multiplier"]
  }
}

```

* **前端視覺呈現**：
改裝成功的醫療器材在 Canvas 的所有視圖中，其基礎渲染色彩將會被即時替換為 AI 回傳的 `neonVisualColor`，且周圍會被包裹一層高頻率閃爍的量子交織粒子特效。在「技術除錯網格視圖」中，該設備將獲得由大模型賦予的全新主動防禦技能。

### 6.2 AI 功能 B：AI 多人對話式「生物醫學工單交班廣播」TTS 系統 (Radio Logs TTS)

為了消滅密密麻麻的純文字歷史日誌流，本功能將全院過去 24 小時的資產監控數據（如：「3 台設備離線、ICU 爆發網絡漏洞攻擊、2 名工程師完成了巡檢」），自動轉化為一段聽覺層面的「多人交班情境廣播劇」。

* **後端路由**：`POST /api/gemini/expedition-podcast`
* **雙階段執行流水線**：
* **第一階段（劇本撰寫）**：將全域 `HospitalGameState` 的歷史日誌打包發送至 `gemini-3.5-flash`。指定提示詞：「你是一位專業的醫療劇編劇。請將以下枯燥的醫材告警數據，轉化為一段醫院資產管理科主管『老張』與急診室護理長『小李』之間的專業、帶有緊迫感的交班對話劇本。格式必須嚴格採用角色名字前導。」
* **第二階段（多語音合成）**：將產出的劇本文字無縫送入 `gemini-3.1-flash-tts-preview` 模型。利用 `multiSpeakerVoiceConfig` 特性，為兩位角色分派完全不同音色與頻率的原廠預置語音：



```typescript
const response = await ai.models.generateContent({
  model: "gemini-3.1-flash-tts-preview",
  contents: [{ parts: [{ text: dialogueScript }] }],
  config: {
    responseModalities: ['AUDIO'],
    speechConfig: {
      multiSpeakerVoiceConfig: {
        speakerVoiceConfigs: [
          { speaker: '老張', voiceConfig: { prebuiltVoiceConfig: { voiceName: 'Kore' } } },
          { speaker: '小李', voiceConfig: { prebuiltVoiceConfig: { voiceName: 'Puck' } } }
        ]
      }
    }
  }
});

```

* **前端整合**：
後端將累積的音訊二進位數據以 Base64 編碼格式串流回傳給前端。React 介面底端會彈出一個復古的「護理站調度收音機」視覺組件。播放時，兩位說話者的頭像會隨著音訊振幅動態進行縮放跳動，右側則以 JetBrains Mono 等寬字體同步滾動顯示對話字幕。

### 6.3 AI 功能 C：AI 臨床環境壓力自適應音效合成器 (Dynamic Soundtrack Synthesizer)

長期處於警報聲頻發的醫療環境中容易引發臨床護理人員的「警報疲勞（Alarm Fatigue）」。本功能拋棄了傳統單調重覆的背景音樂 MP3，直接使用 Google 專屬的音樂大模型，依據當前系統的運作負載與故障嚴重性，實時合成 30 秒的環境白噪音與提示音效迴圈。

* **後端路由**：`POST /api/gemini/generate-soundtrack`
* **核心模型**：`lyria-3-clip-preview`
* **智慧提示詞矩陣**：
* 當系統狀態為 `NORMAL` 時，提示詞為 `"Generate a 30-second low-fi medical telemetry ambiance with soft, regular biometric bleeps and calming pink noise pads suitable for deep concentration."`
* 當系統狀態轉為緊急的 `SURGE` 或遭受攻擊時，提示詞自動重寫為 `"Generate a 30-second tense, rhythmic industrial techno loop mixed with clear, non-fatiguing auditory alert tones complying with international medical alarm standards, 120 BPM."`


* **串流解碼與無縫循環播放**：
後端透過 `generateContentStream` 開啟高性能音訊通道，將 Lyria 模型返回的二進位位元組碎片不斷拼接。前端接收到完整的 Base64 字串後，執行底層二進位反編碼，將其轉化為 Audio WAV Blob 物件，並交由網頁音訊上下文（Web Audio API）執行精準的交叉淡入淡出（Cross-fade）無縫循環播放：

```typescript
const binaryStr = atob(audioBase64);
const bytes = new Uint8Array(binaryStr.length);
for (let i = 0; i < binaryStr.length; i++) {
  bytes[i] = binaryStr.charCodeAt(i);
}
const blob = new Blob([bytes], { type: "audio/wav" });
const audioUrl = URL.createObjectURL(blob);
const audioCtx = new (window.AudioContext || (window as any).webkitAudioContext)();
// 後續將音訊節點掛載至 BiquadFilterNode 執行高通濾波以消除雜音

```

---

## 7. 技術漏洞剖析、效能瓶頸與架構修正案 (Technical Bugs & Remediation)

為了保證本系統在商用環境下的絕對穩定，本節針對原系統中潛在的重大技術架構缺陷、記憶體洩漏與渲染掉幀問題，進行深度源碼級別的診斷並給出最終修正解決方案。

### 7.1 致命漏洞 1：`App.tsx` 中計時器觸發的巢狀狀態更新死鎖 (Nested State Update Cycle)

* **漏洞根源分析**：
在原系統的設計中，巡檢計時器定時檢查目前的工單進度。當累計的巡檢距離超過 `targetProgress` 時，會觸發工單完成邏輯。然而，原代碼在 `setGameState` 的回呼函數內部，直接同步呼叫了外部的資產調度增益函數 `onAddModule()`：

```typescript
// 錯誤的原生代碼邏輯
setGameState((prev) => {
  const nextOrder = { ...prev.activeOrder };
  if (nextOrder && nextOrder.currentProgress >= nextOrder.targetProgress) {
    onAddModule("Critical"); // 災難：在 React 狀態更新執行期間再次引發新的狀態變更！
    return { ...prev, activeOrder: undefined };
  }
  return prev;
});

```

這直接違反了 React 的單向資料流原則。`onAddModule` 內部又包含了自身的 `setGameState` 呼叫，導致 React 的 Fiber 調度器陷入死循環，主執行緒瞬間被阻塞，網頁直接崩潰凍結。

* **架構修正方案**：
將業務邏輯解耦。在 `setGameState` 內部僅執行純粹的狀態資料比對與變更，將隨之引發的副作用（Side Effects）徹底抽離至獨立的 `useEffect` 監聽器中，並引入防抖閥門（Lock Flag），確保狀態更新在單次事件循環內完全清空：

```typescript
// 修正後的架構設計
useEffect(() => {
  if (!gameState.activeOrder) return;
  
  if (gameState.activeOrder.currentProgress >= gameState.activeOrder.targetProgress) {
    // 透過副作用異步分離觸發，阻斷巢狀更新
    executeOrderCompletion(gameState.activeOrder.complianceReward);
  }
}, [gameState.activeOrder?.currentProgress]);

const executeOrderCompletion = (reward: string) => {
  // 清除舊工單並注入新物資，兩者合併為單次狀態原子操作
  setGameState((prev) => ({
    ...prev,
    activeOrder: undefined,
    inventoryModules: [...prev.inventoryModules, createRewardModule(reward)]
  }));
};

```

### 7.2 性能瓶頸 2：當資產高頻掃描時 Canvas 畫布渲染的嚴重掉幀與記憶體洩漏

* **瓶頸根源分析**：
在「科室走廊視差視圖」與「第一人稱巡檢視圖」中，當醫院規模擴大，單次掃描到的設備、信號發射粒子、背景線條節點數量輕易突破 3000 個。原系統在主要的 `requestAnimationFrame` 迴圈內，直接同步調用所有圖形的 `ctx.beginPath()`、`ctx.arc()`，並高頻執行 `ctx.shadowBlur` 外發光高斯濾鏡。
由於 CPU 必須即時計算每一個粒子的投影坐標，且 Canvas 陰影濾鏡會引發嚴重的 GPU 像素重繪（Overdraw），導致畫面格率從 60fps 驟降至 15fps，行動端瀏覽器發熱嚴重，並因未及時銷毀音訊 Blob 物件而引發記憶體洩漏（Memory Leak）。
* **架構修正方案**：
1. **引進離屏畫布（Offscreen Canvas Cache）技術**：對於科室走廊視圖中的靜態背景層（如手術室、護理站剪影），在初始化時一次性繪製到一塊隱藏的離屏畫布上。在主循環中，直接利用高效能的 `ctx.drawImage(offscreenCanvas, ...)` 進行像素區塊拷貝，使 CPU 計算量直接歸零。
2. **粒子對象池（Object Pooling）與批次繪製（Batch Rendering）**：廢除 `new Particle()` 的頻繁垃圾回收行為。預先配置一個容量為 500 的粒子陣列對象池，重覆利用記憶體空間。對於所有顏色相同的數據粒子，合併為單次 `beginPath()` 呼叫，隨後統一執行 `stroke()`，將 WebGL/2D 繪製上下文的 Context 切換開關開銷降至最低。
3. **音訊釋放優化**：在音效合成器切換歌曲時，必須顯式調用 `URL.revokeObjectURL(oldUrl)`，立即向瀏覽器底層釋放二進位記憶體。



### 7.3 安全與合規修正 3：Gemini API 敏感臨床數據外洩與資安防護 (HIPAA Compliance)

* **風險根源分析**：
雖然本系統定位為數位雙生模擬遊戲，但在實際的醫院部署環境中，終端用戶（護理師或工程師）極易在手動設計地圖或輸入改裝提示詞時，無意間填入真實患者的姓名、病歷號或真實醫材的 FDA 認證保密序號。若後端 Express 直接將這些原始字串原封不動地打包發送至 Google 公有雲的 Gemini API 端點，將直接觸法（例如違反 HIPAA 醫療隱私法案與 GDPR 跨境傳輸限制）。
* **架構修正方案**：
在後端 `server.ts` 的 API 路由層，架設一層輕量級的「醫療數據去識別化監控過濾器（PII Sanitization Middleware）」。利用正規表達式矩陣與關鍵字字典，在高頻字串送出前進行強制遮蔽（Masking）：

```typescript
// 後端安全過濾核心代碼
export function sanitizeClinicalPrompt(rawPrompt: string): string {
  // 1. 屏蔽可能存在的台灣身份證字號/身分標記
  let clean = rawPrompt.replace(/[A-Z][1-2]\d{8}/g, "[PATIENT_ID_MASKED]");
  
  // 2. 屏蔽可能存在的真實醫材專利電子序號 (SN)
  clean = clean.replace(/(SN|Serial|序號)\s*[:：=]\s*[A-Za-z0-9-]+/gi, "SN: [DEVICE_SERIAL_MASKED]");
  
  // 3. 屏蔽常見真實姓名格式 (簡單正則矩陣)
  // 此處僅為示意，生產環境會對接更嚴格的 NER 命名實體識別模型
  
  return clean;
}

```

所有端點（如 `/api/gemini/generate-map` 與 `/api/gemini/refine-character`）在調用 `@google/genai` 之前，必須強制執行 `req.body.prompt = sanitizeClinicalPrompt(req.body.prompt)`，從根本上確保了架構的法律合規性與網絡安全性。

---

## 8. 轉型後之綜合延伸思考提問 (20 Follow-Up Questions)

為了進一步深化本技術規格書的實戰價值，並為後續的研發迭代提供清晰的導向，以下列出 20 個針對醫療智慧轉型後的深度技術與商業邏輯提問，供系統架構評審會（Architecture Review Board）探討：

1. **核心指標映射**：全局狀態中的 `overallCompliance`（全院資產合規率）在數學上應如何精確加權？是否應將 Class III（如電擊器、呼吸機）的權重設為 Class II（輸液幫浦）的五倍？
2. **斷線應變機制**：當 `HospitalLoadStatus` 進入 `OUTAGE`（網絡/電力中斷）的極端模擬狀態時，前端的 Canvas 繪製迴圈應如何配合調整，以引導用戶切換到手動的離線紙本審計備份模式？
3. **實時定位串接**：若要將目前的「全域資產拓撲雷達視圖」接入真實醫院的 RTLS 系統（例如結合 BLE 藍牙定位或 Wi-Fi 抵達角度 AoA 數據），目前的 `MapData` Schema 需要做出哪些動態緩衝（Buffer）擴充？
4. **AI 穩定度調優**：為了徹底杜絕 `/api/gemini/generate-map` 端點返回損壞的 JSON 結構，我們除了將 Temperature 調低，是否需要引入 `responseSchema` 的 TypeScript 型別自動生成器來實施強約束？
5. **警報規範對齊**：AI 功能 C（Lyria 音效合成）生成的音訊，如何精確通過國際醫療器械安全標準 **IEC 60601-1-8** 關於脈衝群（Pulse Groups）、頻率分量與諧波成分的合規性硬體檢驗？
6. **多角色多語音擴充**：AI 功能 B（交班廣播）目前預置了兩位演職人員。若要擴充至包含「院長行政查房」、「原廠工程師技術回報」等多達五個角色的對話，後端的 Speech Config 拓撲結構該如何動態擴展？
7. **組件改裝邊界**：在 AI 功能 A（智慧改裝組態擴充器）中，如何設計防呆提示詞限制，防止工程師輸入完全不切實際的科幻改裝（例如：「給泵浦加裝反物質推進器」）而破壞醫療模擬系統的嚴謹性？
8. **離線地圖編輯**：`MapEditor.tsx` 在手動繪製模式下，如何利用 HTML5 LocalStorage 或 IndexedDB 實現完整的離線保存機制，以防醫院內部內網環境偶發性的訊號阻斷？
9. **性能分級渲染**：針對運算效能極度受限的醫療專用手持 PDA 終端，目前的 Canvas requestAnimationFrame 是否需要提供一鍵調降至 30fps 或直接切換為純文字表格的「極簡低耗能模式」？
10. **防範警報疲勞**：除了使用 Lyria 合成舒緩音效，系統是否應在全局計時器檢測到使用者連續處於 `Tactical Debug View` 超過 15 分鐘時，強制在畫面上渲染一層視覺濾鏡，提醒醫工人員進行視覺休息？
11. **歷史日誌回溯**：為了滿足醫療審計對「不可篡改性」的要求，現有的 `HospitalGameState` 是否需要對接後端資料庫（如 PostgreSQL），將每一次 AI 生成的維修履歷與工單結果進行時間戳記存檔？
12. **模組熱插拔計算**：當醫材資產加裝了 `AssetModule` 之後，其 `statBonus` 的增益效果在除錯網格視圖中是採線性的固定數值相加，還是採用邊際效益遞減的對數乘數算法？
13. **漏洞演化邏輯**：系統故障與漏洞目標（`SystemVulnerability`）在路徑上是否會隨著時間推移而「自我演化與擴散」？（例如一個未及時處理的韌體漏洞是否會感染相鄰的 `SyncNode`？）
14. **數據吞吐壓力**：當全院虛擬資產規模從 30 台躍升至 3000 台時，Express 後端的 Vite 中間件在開發模式下是否會面臨嚴重的記憶體堆疊溢位？在 Cloud Run 生產環境中應如何配置適當的實例記憶體？
15. **耗材扣減閉環**：當在戰術網格中執行 `Telemetry Reset` 補充頻寬與修復設備電力時，除了消耗頻寬儲備，是否應同時扣減庫存中的實體備品（如電池、感測器）？
16. **去識別化邊界**：後端的 `sanitizeClinicalPrompt` 監控過濾器，如何防止將合法的醫療專業術語（例如特定包含人名的罕見疾病或特定品牌型號）誤判為個人隱私數據而進行錯誤遮蔽？
17. **多院區供應鏈**：若將系統擴展至跨院區的供應鏈流轉，現有的貝氏曲線路徑算法是否能流暢支撐大範圍 Google Maps API 經緯度坐標向 Canvas 歸一化坐標系（0-100%）的精確雙向映射？
18. **前端資安防護**：儲存在客戶端記憶體中的全局狀態 `HospitalGameState`，如何防止被用戶透過瀏覽器控制台（Console）惡意篡改以偽造合規率數據？是否需要引入二進位狀態混淆？
19. **大模型版本平滑升級**：當未來 Google 推出更先進的 `gemini-3.5-pro` 或更新版本時，後端的 `@google/genai` SDK 呼叫代碼如何在不中斷生產環境運作的情況下，進行平滑的模型路由熱切換？
20. **系統商業化落地**：本系統作為一個融合了 JRPG 遊戲化外殼的專業醫材數位雙生應用，其面向大型三甲醫院推廣時，核心的商業價值主張（Value Proposition）應聚焦於「提升醫工培訓趣味性」，還是「降低真實資產的非預期停機時間」？
