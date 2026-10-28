# 01. Cognitive Laws & Empirical Human Perception

> "The computer is a medium whose characteristics are shaped by human cognitive and motor limitations."  
> — Stuart Card, Thomas Moran, & Allen Newell (*The Psychology of Human-Computer Interaction*, 1983)

This module formalizes the empirical psychological and neurological foundations of user interface engineering. Every layout, delay threshold, choice hierarchy, and visual weight in a production design must trace back to these verified human faculties.

---

## 1. Motor Capacity & Target Acquisition: Fitts's Law (1954)

### 1.1 The Empirical Paper
* **Citation:** Fitts, P. M. (1954). *The information capacity of the human motor system in controlling the amplitude of movement.* Journal of Experimental Psychology, 47(6), 381–391.
* **Core Insight:** The time required to rapidly move to a target area is a function of the ratio between the distance to the target and the width of the target.

### 1.2 Mathematical Formulation
The Shannon formulation (Mackenzie, 1992) defines Movement Time ($MT$):

$$MT = a + b \cdot ID = a + b \log_2 \left( \frac{D}{W} + 1 \right)$$

Where:
* $MT$: Movement time (ms)
* $a, b$: Empirical regression constants determined by the input device (mouse, stylus, finger, gaze track). For direct touch, $b \approx 100-140\text{ ms/bit}$; for mice, $b \approx 80-120\text{ ms/bit}$.
* $D$: Distance (amplitude) from starting point to the target center.
* $W$: Width (tolerance) of the target measured along the axis of motion.
* $ID = \log_2\left(\frac{D}{W} + 1\right)$: **Index of Difficulty** measured in bits.

```
       Distance (D) ──────────────────────────────►
[Cursor / Thumb]                                  ┌─────────────┐
                                                  │   Target    │ Height (H)
                                                  │  Width (W)  │
                                                  └─────────────┘
```

### 1.3 Engineering & UI Implications
1. **The Edge & Corner Advantage (Infinite Target Width):**
   * On desktop displays with bounded mouse cursors, targets pinned against the display edge have infinite effective width along the normal vector: $W \to \infty \implies ID \to 0$.
   * Top menus (macOS menu bar), side docks, and bottom pinned action bars are radically faster to acquire than floating buttons.
2. **Direct Touch Target Invariants:**
   * Finger pads flatten dynamically under pressure (average contact patch: $8-10\text{ mm}$).
   * Minimum tap target size: **$44 \times 44\text{ pt}$ (Apple HIG)** or **$48 \times 48\text{ dp}$ (Google Material)**, corresponding to $\approx 9-10\text{ mm}$ of physical screen dimension.
   * Targets with high severity (destructive buttons) must deliberately increase distance $D$ or decrease target prominence to prevent accidental triggering.
3. **Floating Action Buttons (FABs) & Thumb Zones:**
   * Mobile primary controls must be placed in natural thumb sweep arcs (bottom third of screen, opposite to hand dominance or center-aligned).

---

## 2. Decision Latency & Choice Entropy: Hick-Hyman Law (1952)

### 2.1 The Empirical Paper
* **Citation:** Hick, W. E. (1952). *On the rate of gain of information.* Quarterly Journal of Experimental Psychology, 4(1), 11–26.
* **Hyman, R. (1953):** *Stimulus information as a determinant of reaction time.* Journal of Experimental Psychology, 45(3), 188–196.

### 2.2 Mathematical Formulation
Decision reaction time ($RT$) as a function of $n$ equiprobable alternatives:

$$RT = b \cdot H = b \cdot \log_2(n + 1)$$

For non-equiprobable alternatives with probabilities $p_i$:

$$H = \sum_{i=1}^{n} p_i \log_2 \left(\frac{1}{p_i}\right)$$
$$RT = a + b \sum_{i=1}^{n} p_i \log_2 \left(\frac{1}{p_i} + 1\right)$$

Where:
* $H$: Information entropy (bits).
* $a$: Baseline cognitive processing / visual stimulus transmission delay ($\approx 150-200\text{ ms}$).
* $b$: Cognitive processing speed per bit ($\approx 150-200\text{ ms/bit}$).

### 2.3 Engineering & UI Implications
1. **Progressive Disclosure vs. Flat Mega-Menus:**
   * Doubling unorganized choices adds $\approx 150-200\text{ ms}$ of mental hesitation.
   * If a system has 64 items:
     * Flat list: $RT = b \log_2(64 + 1) \approx 6.02 \times b \approx 900\text{ ms}$.
     * Categorized 2-step hierarchy ($8 \times 8$): $RT = b \log_2(8 + 1) + b \log_2(8 + 1) \approx 2 \times (3.17 \times b) \approx 950\text{ ms}$ for search, but visual scanning is orders of magnitude faster because semantic chunking eliminates branches instantly.
2. **Default Bias & Recommended Options:**
   * Pre-selecting or visually isolating a recommended primary action shifts its probability $p_{\text{primary}} \to 0.85$, collapsing decision entropy $H$ toward 0 for the overwhelming majority of interactions.
3. **Command Palettes (`Cmd + K`):**
   * High-entropy flat interfaces must be transformed into fuzzy search filters where users narrow $n$ from thousands to 1–3 in milliseconds via keystrokes.

---

## 3. Working Memory Limits & Information Chunking: Miller (1956) & Cowan (2001)

### 3.1 The Empirical Papers
* **Miller, G. A. (1956):** *The Magical Number Seven, Plus or Minus Two: Some Limits on Our Capacity for Processing Information.* Psychological Review, 63(2), 81–97.
* **Cowan, N. (2001):** *The magical number 4 in short-term memory: A reconsideration of mental storage capacity.* Behavioral and Brain Sciences, 24(1), 87–114.

### 3.2 Cognitive Model
* Short-Term Memory (STM) is limited not by raw bits, but by **discrete chunks** (meaningful semantic units).
* Modern cognitive science updates Miller's $7 \pm 2$ to **$4 \pm 1$ chunks** when rehearsal and semantic association are prevented (pure working memory load).

### 3.3 Engineering & UI Implications
1. **Input Masking & Form Design:**
   * Unformatted phone number `14155552671` (11 raw chunks $\implies$ immediate recall error).
   * Formatted/chunked: `+1 (415) 555-2671` (3 chunks: Country + Area + Local $\implies$ near 0 recall error).
   * Credit card formatting: `4532 8920 1204 9012` ($4 \times 4$ blocks).
2. **Navigation Bar Density:**
   * Primary top-level navigation items must never exceed $5 \pm 2$ items. Anything beyond 7 must be aggregated into contextual drawers, nested tabs, or overflow menus.
3. **Dashboard Information Density:**
   * Group related metrics into spatial clusters (cards/islands) with distinct visual enclosures. A user can process 4 cards containing 3 metrics each far faster than 12 un-enclosed floating numbers.

---

## 4. System Response Latency & Operational Flow: Doherty & Thadani (1982)

### 4.1 The Empirical Paper
* **Citation:** Doherty, W. J., & Thadani, A. J. (1982). *The Economic Value of Rapid Response Time.* IBM Systems Journal, 21(4), 381–398.
* **The Doherty Threshold:** When conversational interaction between computer and user drops below **400 ms**, user productivity increases nonlinearly; users enter a state of psychological "flow" where thought processes are not disrupted by memory decay.

### 4.2 Latency Thresholds (Miller 1968, Card et al. 1983, Nielsen 1993)

| Latency Window | Human Perception | Interface Requirement |
|---|---|---|
| **$< 16\text{ ms}$ (60-120fps)** | Sub-perceptual / Physical reality | Direct manipulation, dragging, rubber-banding, scroll physics. |
| **$< 100\text{ ms}$** | Instantaneous reaction | Button hover, active press states, toggle flips. No spinner permitted. |
| **$100\text{ ms} - 300\text{ ms}$** | Perceptible pause, but continuous | Modal open, panel slide, tab switch. Smooth spatial transition required. |
| **$300\text{ ms} - 1000\text{ ms}$** | Interruption of thought stream | Skeletons or contextual micro-loaders required. User maintains focus. |
| **$> 1000\text{ ms} (1\text{s})$** | Mental task switch / distraction | Explicit progress bar with percentage or step indicators. |
| **$> 10000\text{ ms} (10\text{s})$** | Total context loss | Background asynchronous processing with notification upon completion. |

### 4.3 Engineering & UI Implications
1. **Optimistic UI Updates:**
   * Mutate the UI immediately ($< 50\text{ ms}$) on client action (e.g., liking a post, toggling a setting).
   * Revert with polite toast notification only on network rejection ($0.01\%$ case).
2. **Skeleton Screens vs. Spinners:**
   * Centered spinning loaders focus human attention on elapsed time, increasing perceived waiting time by up to 30%.
   * Dimensionally stable skeleton screens simulate progressive rendering, preserving spatial orientation.

---

## 5. Cognitive Load Theory: Sweller (1988)

### 5.1 The Empirical Paper
* **Citation:** Sweller, J. (1988). *Cognitive load during problem solving: Effects on learning.* Cognitive Science, 12(2), 257–285.

### 5.2 Cognitive Architecture
Total Cognitive Load ($CL_{\text{total}}$) is divided into three components:

$$CL_{\text{total}} = CL_{\text{intrinsic}} + CL_{\text{germane}} + CL_{\text{extraneous}}$$

* **Intrinsic Load:** The inherent difficulty of the domain task (e.g., calculating tax liabilities or editing a video). Unavoidable.
* **Germane Load:** The mental effort dedicated to processing information and constructing schemas (useful learning).
* **Extraneous Load:** The mental effort wasted decoding poor UI layout, low-contrast text, confusing icons, ambiguous navigation, or visual noise. **Must be driven to zero.**

### 5.3 UI Invariants to Eliminate Extraneous Load
1. **Visual Noise Reduction (The 1-Border Rule):**
   * Do not combine a border, a drop shadow, a background color contrast, AND bold typography to separate an element. Choose **one** clear demarcation technique.
2. **Recognition over Recall (Nielsen Heuristic #6):**
   * Never require users to remember information across screen boundaries (e.g., product SKU from Step 1 displayed during Step 3 checkout).
3. **Split-Attention Effect:**
   * Form error messages must appear adjacent to the exact offending input, never grouped in a summary banner at the top of the page.

---

## 6. GOMS & Keystroke-Level Modeling: Card, Moran, & Newell (1980, 1983)

### 6.1 The Seminal Book
* **Card, S. K., Moran, T. P., & Newell, A. (1983):* *The Psychology of Human-Computer Interaction.* Lawrence Erlbaum Associates.

### 6.2 Keystroke-Level Model (KLM) Operators
Execution time $T_{\text{execute}}$ for an expert routine task is modeled as:

$$T_{\text{execute}} = \sum T_K + \sum T_P + \sum T_H + \sum T_D + \sum T_M + \sum T_R$$

* **$K$ (Keystroke / Button Press):** $\approx 0.12 - 0.28\text{ s}$ (depending on typing speed; standard baseline $0.20\text{ s}$).
* **$P$ (Point to target with mouse):** Governed by Fitts's Law; empirical default $\approx 1.10\text{ s}$.
* **$H$ (Homing hands between keyboard and mouse):** $\approx 0.40\text{ s}$.
* **$M$ (Mental preparation / decision):** $\approx 1.20 - 1.35\text{ s}$.
* **$R(t)$ (System Response Time):** Delay where user waits for the computer.

```
Example Task: Searching a user list via mouse vs. keyboard shortcut:
- Mouse: [Homing to mouse: 0.4s] + [Point to Search: 1.1s] + [Click: 0.2s] + [Mental: 1.2s] = 2.90s
- Keyboard: [Cmd+K: 0.2s] + [Mental: 0.4s] = 0.60s (4.8x faster)
```

---

## 7. Usability Heuristics: Nielsen & Molich (1990)

### 7.1 The Paper
* **Citation:** Nielsen, J., & Molich, R. (1990). *Heuristic evaluation of user interfaces.* Proceedings of the SIGCHI Conference on Human Factors in Computing Systems (CHI '90), 249–256.

### 7.2 The 10 Invariant Heuristics
1. **Visibility of system status:** Always keep users informed through appropriate, immediate feedback within $< 100\text{ ms}$.
2. **Match between system and real world:** Speak the user’s language (phrases, mental models) rather than system-oriented internal jargon.
3. **User control and freedom:** Provide clear "emergency exits" (Undo, Redo, Cancel, Esc) without convoluted confirmations.
4. **Consistency and standards:** Follow platform conventions (Mac vs. Windows, Web vs. iOS). Do not invent novel UI patterns for solved problems.
5. **Error prevention:** Eliminate error-prone conditions before they happen (disable invalid states, enforce constraints, offer suggestions).
6. **Recognition rather than recall:** Minimize memory load by making objects, actions, and options visible.
7. **Flexibility and efficiency of use:** Provide accelerators (keyboard shortcuts, batch actions) for expert users while keeping basic paths simple.
8. **Aesthetic and minimalist design:** Dialogues should not contain irrelevant or rarely needed information (every extra unit of information competes with relevant units).
9. **Help users recognize, diagnose, and recover from errors:** Plain language error messages indicating the exact problem and constructive solution.
10. **Help and documentation:** Contextual, searchable, task-focused assistance delivered at the moment of need.

---

## 8. Empirical Neuro-Aesthetics & The Aesthetic-Usability Effect

### 8.1 Kurosu & Kashimura (1995) & Tractinsky (1997, 2000)
* **Citations:**
  * Kurosu, M., & Kashimura, K. (1995). *Apparent usability vs. inherent usability.* CHI '95 Conference Companion, 292–293.
  * Tractinsky, N. (1997). *Aesthetics and apparent usability: investigating the fit.* CHI '97, 115–122.
  * Tractinsky, N., Katz, A. S., & Ikar, D. (2000). *What is beautiful is usable.* Interacting with Computers, 13(2), 127–145.
* **Finding:** Beautiful interfaces are perceived as significantly easier to use, and users exhibit higher tolerance for minor functional hurdles. The correlation between perceived aesthetics and perceived usability remains constant **both before and after actual product usage**.

### 8.2 The 50-Millisecond First Impression: Lindgaard et al. (2006)
* **Citation:** Lindgaard, G., Fernandes, G., Dudek, C., & Brown, J. (2006). *Attention web designers: You have 50 milliseconds to make a good first impression!* Behaviour & Information Technology, 25(2), 115–126.
* **Finding:** Visual appeal judgments are formed within **50 milliseconds** ($\frac{1}{20}\text{th}$ of a second). This cognitive reaction occurs via the visual cortex and amygdala before the conscious prefrontal cortex can read a single word of text.
* **Implication:** Visual polish—typography baseline alignment, harmonious color contrast, crisp borders, and zero-FOUT typography—dictates the user's subconscious trust score before textual comprehension begins.

### 8.3 Visual Complexity & Colorfulness: Reinecke et al. (2013)
* **Citation:** Reinecke, K. et al. (2013). *Predicting users' first impressions of website aesthetics using visual complexity and colorfulness.* CHI '13, 2049–2058.
* **Empirical Findings:**
  * **Visual Complexity:** Displays a strict inverted-U curve. Interfaces with either extremely low structure or high visual clutter score poorly. High-aesthetic sites feature high structural clarity with minimal visual entropy.
  * **Colorfulness:** Uncontrolled multi-hue colorfulness causes immediate aesthetic rejection. Superior ratings occur with unified, low-to-moderate colorfulness paired with a single intentional chromatic accent.
