# Quick EMOM Workout Generator — Implementation Spec

## Overview

Add a "Quick EMOM" panel to `timer.html` that lets users generate a random EMOM (Every Minute On the Minute) workout from the embedded exercise library. The user makes a few quick selections via toggle-chip buttons, accepts a disclaimer, previews the generated workout, then starts it — all without touching the manual timer controls.

The EMOM panel lives **above** the existing `#controls` section inside `<main class="container">`, as its own collapsible `<section class="card stack">`. The existing manual timer functionality is **completely untouched** — Quick EMOM is additive only.

---

## 1. Exercise Database

Embed the full EMOM-eligible exercise library (127 exercises, ~30KB) as a JavaScript array called `EMOM_DB` inside the existing `<script>` block, right after the Service Worker registration and PWA Install sections (before the `// ---------- Utilities ----------` comment).

Each exercise object has these keys (shortened to save bytes):
- `e` — Equipment (string): `"Bodyweight"`, `"Kettlebell"`, `"Dumbbell"`, `"Resistance Bands"`, `"TRX"`, `"Stability Ball"`, `"Slam Ball"`, `"Step"`, `"Sturdy Chair"`
- `s` — Secondary Equipment (string, may be empty): `"Dumbbell"`, `"Kettlebell"`, `"Step"`, or `""`
- `n` — Exercise Name (string): e.g. `"KB Swing"`, `"Push-ups"`
- `d` — Difficulty (string): `"Easy"`, `"Intermediate"`, `"Advanced"`
- `b` — Body Part (string): `"Upper"`, `"Lower"`, `"Core"`, `"Full Body"`
- `r` — Reps (string): e.g. `"10"`, `"5 per side"`
- `i` — Instructions (string): full coaching cue

The JSON data is provided in the companion file `exercises.json`. Embed it as:
```js
const EMOM_DB = [ /* paste JSON array contents here */ ];
```

---

## 2. HTML Structure

Insert this new section inside `<main class="container">`, **before** the existing `<section class="card stack" id="controls">`:

```
<!-- Quick EMOM Generator -->
<section class="card stack" id="emomPanel">
  <div class="row" style="justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px">
    <div style="display:flex; align-items:center; gap:10px">
      <span style="font-size:20px">⚡</span>
      <div>
        <div style="font-weight:700; font-size:15px;">Quick EMOM</div>
        <div class="help">Generate a random EMOM workout in seconds</div>
      </div>
    </div>
    <button class="btn" id="emomToggleBtn">Hide</button>
  </div>

  <div id="emomBody">
    <!-- TIME -->
    <div class="emom-group">
      <div class="emom-group-label">Time (minutes)</div>
      <div class="emom-chips" id="emomTime">
        <!-- Single-select: one active at a time -->
        <button class="emom-chip active" data-val="15">15</button>
        <button class="emom-chip" data-val="20">20</button>
        <button class="emom-chip" data-val="25">25</button>
        <button class="emom-chip" data-val="30">30</button>
        <button class="emom-chip" data-val="35">35</button>
        <button class="emom-chip" data-val="40">40</button>
      </div>
    </div>

    <!-- EQUIPMENT -->
    <div class="emom-group">
      <div class="emom-group-label">Equipment <span class="help" style="font-weight:400">(select all you have)</span></div>
      <div class="emom-chips multi" id="emomEquip">
        <button class="emom-chip active" data-val="Bodyweight">Bodyweight</button>
        <button class="emom-chip" data-val="Kettlebell">Kettlebell</button>
        <button class="emom-chip" data-val="Dumbbell">Dumbbell</button>
        <button class="emom-chip" data-val="Resistance Bands">Bands</button>
        <button class="emom-chip" data-val="TRX">TRX</button>
        <button class="emom-chip" data-val="Stability Ball">Stability Ball</button>
        <button class="emom-chip" data-val="Slam Ball">Slam Ball</button>
        <button class="emom-chip" data-val="Step">Step</button>
        <button class="emom-chip" data-val="Sturdy Chair">Sturdy Chair</button>
      </div>
    </div>

    <!-- DIFFICULTY -->
    <div class="emom-group">
      <div class="emom-group-label">Difficulty <span class="help" style="font-weight:400">(select all that apply)</span></div>
      <div class="emom-chips multi" id="emomDiff">
        <button class="emom-chip active" data-val="Easy">Easy</button>
        <button class="emom-chip active" data-val="Intermediate">Intermediate</button>
        <button class="emom-chip" data-val="Advanced">Advanced</button>
      </div>
    </div>

    <!-- BODY PART -->
    <div class="emom-group">
      <div class="emom-group-label">Focus <span class="help" style="font-weight:400">(select all that apply)</span></div>
      <div class="emom-chips multi" id="emomBody">
        <button class="emom-chip active" data-val="Upper">Upper</button>
        <button class="emom-chip active" data-val="Lower">Lower</button>
        <button class="emom-chip active" data-val="Core">Core</button>
        <button class="emom-chip active" data-val="Full Body">Full Body</button>
      </div>
    </div>

    <!-- GENERATE BUTTON -->
    <button class="btn primary" id="emomGenerateBtn" style="width:100%; padding:14px; font-size:15px; margin-top:4px;">
      ⚡ Generate Workout
    </button>

    <!-- PREVIEW (hidden until generated) -->
    <div id="emomPreview" style="display:none">
      <div class="sep"></div>
      <div id="emomPreviewContent"></div>
      <div style="display:flex; gap:8px; margin-top:12px; flex-wrap:wrap">
        <button class="btn" id="emomRerollBtn" style="flex:1;">🎲 Re-roll</button>
        <button class="btn primary" id="emomStartBtn" style="flex:2; padding:14px; font-size:15px;">▶ Start EMOM</button>
      </div>
      <button class="btn ghost" id="emomAskAssistantBtn" style="width:100%; margin-top:8px; font-size:12px; color:var(--muted);">
        🤖 Need help? Ask my assistant to explain an exercise
      </button>
    </div>

    <!-- NO MATCHES MESSAGE (hidden by default) -->
    <div id="emomNoMatch" style="display:none; text-align:center; padding:16px; color:var(--danger); font-size:13px;">
      No exercises match your selections. Try adding more equipment or broadening the difficulty.
    </div>
  </div>
</section>
```

**IMPORTANT ID COLLISION**: The body part chips container cannot be `id="emomBody"` because the outer collapsible div already uses that id. Rename the body part chips container to `id="emomFocus"` and the outer collapsible div to `id="emomInner"`. Update all JS references accordingly.

---

## 3. CSS

Add these styles in the existing `<style>` block, after the `.pill` styles and before the `/* Validation */` comment:

```css
/* ====== Quick EMOM ====== */
.emom-group { display:grid; gap:6px; }
.emom-group-label { font-size:13px; color:var(--muted); font-weight:600; }
.emom-chips { display:flex; flex-wrap:wrap; gap:6px; }
.emom-chip {
  background:#10172b;
  border:1px solid var(--border);
  color:var(--muted);
  padding:8px 14px;
  border-radius:10px;
  font-size:13px;
  font-weight:600;
  cursor:pointer;
  transition: background .12s, color .12s, border-color .12s;
  user-select:none;
  white-space:nowrap;
}
.emom-chip:hover { background:#16213a; color:var(--fg); }
.emom-chip.active {
  background:var(--accent);
  color:#0b1020;
  border-color:var(--accent);
}

/* EMOM Preview exercise list */
.emom-exercise-row {
  display:grid;
  grid-template-columns:auto 1fr auto;
  gap:10px;
  align-items:start;
  padding:10px 0;
  border-bottom:1px solid var(--border);
}
.emom-exercise-row:last-child { border-bottom:none; }
.emom-exercise-num {
  width:28px; height:28px;
  border-radius:50%;
  display:grid; place-items:center;
  font-size:12px; font-weight:700;
  color:#0b1020;
  flex-shrink:0;
}
.emom-exercise-name { font-weight:600; font-size:14px; }
.emom-exercise-meta { font-size:12px; color:var(--muted); margin-top:2px; }
.emom-exercise-reps { font-size:13px; font-weight:600; color:var(--accent); white-space:nowrap; }
.emom-preview-header {
  font-size:13px; color:var(--muted); margin-bottom:4px;
  display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:6px;
}
.emom-preview-header strong { color:var(--fg); font-size:15px; }

/* EMOM Disclaimer Modal */
.emom-modal-overlay {
  position:fixed; inset:0; background:rgba(0,0,0,.7);
  display:grid; place-items:center; z-index:1000;
  padding:16px;
  backdrop-filter:blur(4px);
  -webkit-backdrop-filter:blur(4px);
}
.emom-modal {
  background:var(--card); border:1px solid var(--border);
  border-radius:16px; padding:24px;
  max-width:480px; width:100%;
  max-height:90vh; overflow-y:auto;
}
.emom-modal h3 { margin:0 0 12px; font-size:16px; color:var(--fg); }
.emom-modal p { font-size:13px; color:var(--muted); line-height:1.5; margin:0 0 16px; }
.emom-modal .btn { width:100%; margin-top:8px; }

/* Touch targets for chips */
@media (pointer: coarse) {
  .emom-chip { min-height:44px; padding:10px 16px; }
}
```

---

## 4. JavaScript — Chip Toggle Logic

Add this in the JS section, after the EMOM_DB declaration:

```js
// ---------- Quick EMOM Generator ----------

// Chip toggle behaviour
document.querySelectorAll('.emom-chips').forEach(container => {
  const isMulti = container.classList.contains('multi');
  container.addEventListener('click', e => {
    const chip = e.target.closest('.emom-chip');
    if (!chip) return;
    if (isMulti) {
      // Multi-select: toggle on/off
      chip.classList.toggle('active');
    } else {
      // Single-select: only one active
      container.querySelectorAll('.emom-chip').forEach(c => c.classList.remove('active'));
      chip.classList.add('active');
    }
  });
});
```

**Time** is single-select (only one active at a time, default `15`).
**Equipment, Difficulty, Body Part** are all multi-select (toggle each on/off).

---

## 5. JavaScript — EMOM Toggle (Show/Hide)

```js
const emomPanel = document.getElementById('emomPanel');
const emomToggleBtn = document.getElementById('emomToggleBtn');
const emomInner = document.getElementById('emomInner');

emomToggleBtn.addEventListener('click', () => {
  const isHidden = emomInner.style.display === 'none';
  emomInner.style.display = isHidden ? '' : 'none';
  emomToggleBtn.textContent = isHidden ? 'Hide' : 'Show';
});
```

---

## 6. JavaScript — Helper Functions

```js
function getActiveVals(containerId) {
  return Array.from(document.querySelectorAll(`#${containerId} .emom-chip.active`))
    .map(c => c.dataset.val);
}

// Fisher-Yates shuffle
function shuffle(arr) {
  const a = [...arr];
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}
```

---

## 7. JavaScript — EMOM Generation Algorithm

### EMOM timing model

In EMOM, each exercise gets exactly **1 minute** (do your reps, rest for the remainder of the minute). Between sets there is a **1 minute rest**.

```
totalMinutes = (exercises × sets) + (sets − 1) × 1
```

Rearranged:
```
exercises × sets = totalMinutes − (sets − 1)
workMinutes = totalMinutes − sets + 1
```

The generator should try combinations of 4–6 exercises × 3–5 sets that exactly hit the target time. It should prefer more exercises and fewer sets (more variety is more fun).

### Filter & select logic

```js
function generateEMOM() {
  const totalMin = Number(getActiveVals('emomTime')[0] || 20);
  const equipSel = getActiveVals('emomEquip');
  const diffSel = getActiveVals('emomDiff');
  const bodySel = getActiveVals('emomFocus');

  if (!equipSel.length || !diffSel.length || !bodySel.length) {
    showEmomNoMatch('Select at least one option in each category.');
    return null;
  }

  // Filter exercises
  const pool = EMOM_DB.filter(ex => {
    // Equipment: primary must be in selection, AND if secondary exists it must also be in selection
    if (!equipSel.includes(ex.e)) return false;
    if (ex.s && !equipSel.includes(ex.s)) return false;
    // Difficulty
    if (!diffSel.includes(ex.d)) return false;
    // Body part
    if (!bodySel.includes(ex.b)) return false;
    return true;
  });

  if (pool.length < 4) {
    showEmomNoMatch(`Only ${pool.length} exercise(s) match — need at least 4. Broaden your selections.`);
    return null;
  }

  // Find valid (exercises, sets) combos where exercises is 4-6, sets is 3-5
  // totalMin = exercises * sets + (sets - 1)
  // exercises * sets = totalMin - sets + 1
  const combos = [];
  for (let ex = 6; ex >= 4; ex--) {
    for (let sets = 3; sets <= 5; sets++) {
      const workMin = ex * sets;
      const restMin = sets - 1;
      if (workMin + restMin === totalMin) {
        combos.push({ exercises: ex, sets });
      }
    }
  }

  if (!combos.length) {
    // Fallback: find closest fit
    // Try to get as close as possible to totalMin
    let best = null;
    let bestDiff = Infinity;
    for (let ex = 6; ex >= 4; ex--) {
      for (let sets = 3; sets <= 5; sets++) {
        const actual = ex * sets + sets - 1;
        const diff = Math.abs(actual - totalMin);
        if (diff < bestDiff || (diff === bestDiff && ex > (best?.exercises || 0))) {
          bestDiff = diff;
          best = { exercises: ex, sets };
        }
      }
    }
    if (best) combos.push(best);
  }

  // Prefer first combo (most exercises due to sort order)
  const chosen = combos[0];
  const numEx = Math.min(chosen.exercises, pool.length);

  // Select exercises with body part diversity
  const selected = selectDiverse(pool, numEx, bodySel);

  const actualTotal = numEx * chosen.sets + (chosen.sets - 1);

  return {
    exercises: selected,
    sets: chosen.sets,
    numExercises: numEx,
    totalMinutes: actualTotal,
    targetMinutes: totalMin
  };
}
```

### Body part diversity selection

The generator should try to spread exercises across the selected body parts rather than picking 6 leg exercises:

```js
function selectDiverse(pool, count, bodyParts) {
  const shuffled = shuffle(pool);
  const selected = [];
  const usedNames = new Set();

  // Round-robin through body parts
  let bpIndex = 0;
  const bpList = shuffle([...bodyParts]);

  // First pass: one per body part (round-robin)
  while (selected.length < count) {
    const targetBP = bpList[bpIndex % bpList.length];
    const candidate = shuffled.find(ex =>
      ex.b === targetBP && !usedNames.has(ex.n)
    );
    if (candidate) {
      selected.push(candidate);
      usedNames.add(candidate.n);
    }
    bpIndex++;

    // Safety: if we've cycled through all body parts without finding anything, fill from remainder
    if (bpIndex > count * bpList.length) {
      const remaining = shuffled.filter(ex => !usedNames.has(ex.n));
      for (const ex of remaining) {
        if (selected.length >= count) break;
        selected.push(ex);
        usedNames.add(ex.n);
      }
      break;
    }
  }

  return selected;
}
```

---

## 8. JavaScript — Disclaimer Modal

The disclaimer must appear **once per session** (use a session flag, not localStorage — we want them to see it each visit). It shows **before** the preview is displayed for the first time.

```js
let emomDisclaimerAccepted = false;

function showDisclaimer() {
  return new Promise((resolve) => {
    const overlay = document.createElement('div');
    overlay.className = 'emom-modal-overlay';
    overlay.innerHTML = `
      <div class="emom-modal">
        <h3>⚠️ Before You Begin</h3>
        <p>
          This workout has been <strong>randomly generated</strong> by an algorithm.
          It is <strong>not</strong> a personalised recommendation from Scott Crawford
          or Crawford Coaching.
        </p>
        <p>
          Exercise carries inherent risks including injury and aggravation of
          pre-existing conditions. <strong>Consult your doctor</strong> before starting
          any new exercise program, especially if you have health concerns.
        </p>
        <p>
          By proceeding you confirm that you understand these risks, take full
          responsibility for your own safety, and release Crawford Coaching from
          any liability.
        </p>
        <button class="btn primary" id="emomDisclaimerAccept">I Understand — Show My Workout</button>
        <button class="btn ghost" id="emomDisclaimerCancel" style="color:var(--muted);">Cancel</button>
      </div>
    `;
    document.body.appendChild(overlay);

    overlay.querySelector('#emomDisclaimerAccept').addEventListener('click', () => {
      emomDisclaimerAccepted = true;
      overlay.remove();
      resolve(true);
    });
    overlay.querySelector('#emomDisclaimerCancel').addEventListener('click', () => {
      overlay.remove();
      resolve(false);
    });
    // Clicking the backdrop also cancels
    overlay.addEventListener('click', (e) => {
      if (e.target === overlay) { overlay.remove(); resolve(false); }
    });
  });
}
```

---

## 9. JavaScript — Generate Button Flow

```js
const emomGenerateBtn = document.getElementById('emomGenerateBtn');
const emomRerollBtn = document.getElementById('emomRerollBtn');
const emomPreview = document.getElementById('emomPreview');
const emomPreviewContent = document.getElementById('emomPreviewContent');
const emomNoMatch = document.getElementById('emomNoMatch');
const emomStartBtn = document.getElementById('emomStartBtn');
const emomAskAssistantBtn = document.getElementById('emomAskAssistantBtn');

let currentEmomPlan = null;

function showEmomNoMatch(msg) {
  emomNoMatch.textContent = msg;
  emomNoMatch.style.display = '';
  emomPreview.style.display = 'none';
}

function renderEmomPreview(plan) {
  emomNoMatch.style.display = 'none';
  emomPreview.style.display = '';

  const timeNote = plan.totalMinutes !== plan.targetMinutes
    ? ` <span style="color:var(--muted); font-size:12px;">(closest fit to ${plan.targetMinutes}min)</span>`
    : '';

  let html = `
    <div class="emom-preview-header">
      <strong>EMOM ${plan.totalMinutes} min</strong>
      <span>${plan.numExercises} exercises × ${plan.sets} sets • 1 min rest between sets${timeNote}</span>
    </div>
  `;

  plan.exercises.forEach((ex, i) => {
    const color = PALETTE[i % PALETTE.length];
    html += `
      <div class="emom-exercise-row">
        <div class="emom-exercise-num" style="background:${color}">${i + 1}</div>
        <div>
          <div class="emom-exercise-name">${ex.n}</div>
          <div class="emom-exercise-meta">${ex.e}${ex.s ? ' + ' + ex.s : ''} • ${ex.b} • ${ex.d}</div>
        </div>
        <div class="emom-exercise-reps">${ex.r}</div>
      </div>
    `;
  });

  html += `<div class="help" style="margin-top:8px; text-align:center;">
    Each exercise = 1 minute (do the reps, rest for the remainder)
  </div>`;

  emomPreviewContent.innerHTML = html;
}

async function handleGenerate() {
  // Show disclaimer on first generate of the session
  if (!emomDisclaimerAccepted) {
    const accepted = await showDisclaimer();
    if (!accepted) return;
  }

  const plan = generateEMOM();
  if (!plan) return;

  currentEmomPlan = plan;
  renderEmomPreview(plan);
}

emomGenerateBtn.addEventListener('click', handleGenerate);
emomRerollBtn.addEventListener('click', handleGenerate);
```

---

## 10. JavaScript — Start EMOM (Inject into Timer Engine)

This is the critical integration point. The EMOM start button builds a plan array in the **exact same format** that `buildPlan()` returns, then injects it directly into the existing timer engine:

```js
function startEmomPlan() {
  if (!currentEmomPlan) return;

  const plan = currentEmomPlan;
  const timerPlan = []; // same format as buildPlan() output

  for (let s = 0; s < plan.sets; s++) {
    for (let i = 0; i < plan.exercises.length; i++) {
      const ex = plan.exercises[i];
      const color = PALETTE[i % PALETTE.length];
      timerPlan.push({
        type: 'work',
        set: s + 1,
        interval: i + 1,
        seconds: 60,
        color: color,
        setName: `Set ${s + 1} of ${plan.sets}`,
        intervalName: `${ex.n} — ${ex.r}`
      });
    }
    // 1 min rest between sets (not after the last set)
    if (s < plan.sets - 1) {
      timerPlan.push({
        type: 'break',
        set: s + 1,
        interval: plan.exercises.length,
        seconds: 60,
        color: breakColor // uses existing break colour variable
      });
    }
  }

  // Set workout name
  workoutName.value = `EMOM ${plan.totalMinutes}min`;

  // Inject into timer engine (same flow as start() but skip validation/buildPlan)
  planState = timerPlan;
  cumDurations = [];
  let acc = 0;
  for (const st of planState) { cumDurations.push(acc); acc += st.seconds; }
  totalPlanSeconds = acc;

  idx = 0;
  running = true;
  startStep();
  cancelAnimationFrame(rafId);
  rafId = requestAnimationFrame(loop);

  startBtn.disabled = true;
  pauseBtn.disabled = false;
  resumeBtn.disabled = true;
  resetBtn.disabled = false;
  statusEl.textContent = 'Running…';

  // Auto-hide panels
  if (controlsPanel.style.display !== 'none') toggleOptions();
  emomInner.style.display = 'none';
  emomToggleBtn.textContent = 'Show';

  // Scroll to timer display
  document.getElementById('display').scrollIntoView({ behavior: 'smooth', block: 'center' });
}

emomStartBtn.addEventListener('click', startEmomPlan);
```

---

## 11. JavaScript — Ask Assistant Button

Simple new-tab link:

```js
emomAskAssistantBtn.addEventListener('click', () => {
  window.open('https://www.crawford-coaching.ca/assistant', '_blank', 'noopener');
});
```

---

## 12. Header Integration

Add a "Quick EMOM" button to the header row (next to the existing "Hide Controls" button) that scrolls to / focuses the EMOM panel:

In the header `<div class="row" style="gap:6px">`, add before the existing buttons:
```html
<button class="btn primary" id="headerEmomBtn" style="padding:8px 14px; font-size:13px;">⚡ Quick EMOM</button>
```

JS:
```js
document.getElementById('headerEmomBtn').addEventListener('click', () => {
  // Show the panel if hidden
  if (emomInner.style.display === 'none') {
    emomInner.style.display = '';
    emomToggleBtn.textContent = 'Hide';
  }
  emomPanel.scrollIntoView({ behavior: 'smooth', block: 'start' });
});
```

---

## 13. Edge Cases & Validation

1. **Secondary equipment filtering**: If an exercise has `s: "Dumbbell"` (secondary equipment), BOTH the primary and secondary must be in the user's equipment selection. e.g., "Stability Ball + Dumbbell: Chest Press" only shows if both Stability Ball AND Dumbbell are selected.

2. **Not enough exercises**: If fewer than 4 exercises match the filters, show the `#emomNoMatch` message with the count and suggestion to broaden filters.

3. **No exact time match**: The combo-finder falls back to the closest fit. The preview header shows `(closest fit to Xmin)` when the actual time differs from the target.

4. **Duplicate exercise names**: The `selectDiverse` function uses `usedNames` set to avoid picking the same exercise name twice (e.g., "Squat" appears under Bodyweight, Kettlebell, Dumbbell, etc. — only one should appear per workout).

5. **Pool smaller than requested exercises**: If the pool has 5 exercises but we want 6, `numEx` is capped to pool size via `Math.min`.

6. **Reset clears EMOM state**: The existing `reset()` function already clears `planState` etc. No changes needed — the EMOM just injects into the same engine.

---

## 14. What NOT to Change

- Do not modify the existing `buildPlan()`, `start()`, `validate()`, or any other timer engine functions
- Do not change the existing CSS variables or design system
- Do not change the existing button styling — reuse `.btn`, `.btn.primary`, `.btn.ghost`, `.btn.danger`
- Do not change the existing card/pill/help class patterns
- Do not modify the favourites system
- Do not modify the service worker or manifest

---

## 15. Exact Combo Table (Reference)

For the developer's reference, here are all valid (exercises × sets) combos for each target time:

| Target | exercises × sets + (sets-1) = total | Exact matches |
|--------|-------------------------------------|---------------|
| 15 min | 4×3+2=14, 5×3+2=17, 4×4+3=19... | **No exact**. Closest: 4×3=14min or 5×3=17min |
| 20 min | 5×3+2=17, 4×4+3=19, **6×3+2=20** | **6×3** ✅ |
| 25 min | **5×4+3=23**, **6×4+3=27**, **4×5+4=24**, **5×5+4=29** | No exact. Closest: 4×5=24 or 5×4=23 or 6×4=27 |
| 30 min | **6×4+3=27**, **5×5+4=29**, **6×5+4=34** | No exact. Closest: 5×5=29 or 6×4=27 |
| 35 min | **6×5+4=34** | No exact. Closest: 6×5=34 |
| 40 min | — | No exact in 4-6 × 3-5 range |

**Observation**: Very few exact matches exist in the 4-6 exercises × 3-5 sets constraint with 1min rest. Consider two alternatives (pick one):

**Option A (recommended)**: Allow the rest between sets to flex. Instead of fixed 1min, calculate rest to fill the gap. e.g., for 25min with 5 exercises × 4 sets = 20min work, distribute 5min across 3 rest periods = 1:40 each.

**Option B**: Widen the range slightly to 4-8 exercises × 3-5 sets, which covers more targets.

**Option C**: Accept approximate fits and show "closest fit" note.

### Recommended approach: Option A — flexible rest

Change the algorithm so rest between sets is calculated to fill remaining time:

```
workMinutes = exercises × sets
restMinutes = totalMinutes − workMinutes
restPerBreak = restMinutes / (sets − 1)     // in seconds: restPerBreak × 60
```

Require `restPerBreak` to be between 0.5 and 2 minutes (30s–120s). This gives much better coverage:

```js
const combos = [];
for (let ex = 6; ex >= 4; ex--) {
  for (let sets = 3; sets <= 5; sets++) {
    const workMin = ex * sets;
    const restMin = totalMin - workMin;
    const breaks = sets - 1;
    if (breaks === 0) continue;
    const restEach = restMin / breaks;
    // Rest per break must be 0.5–2 minutes
    if (restEach >= 0.5 && restEach <= 2) {
      combos.push({ exercises: ex, sets, restSeconds: Math.round(restEach * 60) });
    }
  }
}
// Sort: prefer more exercises, then fewer sets
combos.sort((a, b) => b.exercises - a.exercises || a.sets - b.sets);
```

This way, a 25-minute EMOM with 5 exercises × 4 sets = 20min work + 5min rest across 3 breaks = 1:40 rest between sets. The preview should display the rest duration: "1:40 rest between sets".

Update `startEmomPlan` to use `plan.restSeconds` instead of hardcoded `60` for the break steps.

---

## 16. File Placement

All changes go in `timer.html`. No new files needed. The exercise database is embedded directly in the JS.

---

## 17. Testing Checklist

- [ ] All 6 time options generate a valid workout
- [ ] Equipment filtering respects secondary equipment
- [ ] Difficulty multi-select works (selecting only "Easy" shows easy exercises)
- [ ] Body part multi-select works (selecting only "Core" shows core exercises)
- [ ] Selecting no options in any group shows an error message
- [ ] Disclaimer modal appears on first generate, not on subsequent re-rolls
- [ ] Disclaimer cancel prevents workout from showing
- [ ] Preview shows exercise names, reps, equipment, body part, difficulty
- [ ] Re-roll generates a different selection
- [ ] Start EMOM injects plan and starts timer correctly
- [ ] Timer displays exercise names in the interval name area
- [ ] Pause/Resume/Reset work normally after EMOM start
- [ ] "Ask my assistant" opens crawford-coaching.ca/assistant in new tab
- [ ] EMOM panel hides when timer starts
- [ ] Existing manual timer controls still work independently
- [ ] Mobile touch targets are 44px minimum
- [ ] No JS errors in console
