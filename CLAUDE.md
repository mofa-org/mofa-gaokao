# CLAUDE.md — 圆锥曲线专题 (Conic Sections for Gaokao)

## Project Overview

Interactive conic sections course for Chinese Gaokao (高考数学), designed around cognitive science principles. Content sourced from top Bilibili math creators (一数, 赵礼显), reorganized by learning sequence (not video order), and enhanced with D3.js/Three.js/Desmos interactive visualizations.

**Live site:** https://gaokao.moxin.app/
**Repos:** `ymote/conics` (origin, deploys to gaokao.moxin.app) | `mofa-org/mofa-gaokao` (mirror)

## Tech Stack

| Component | Technology | CDN/Source |
|-----------|-----------|------------|
| Static site generator | **Quarto** | quarto-dev/quarto-actions/setup@v2 |
| 2D animations | **D3.js v7** | `https://d3js.org/d3.v7.min.js` |
| 3D visualizations | **Three.js 0.172** | ES module import map from jsdelivr |
| Interactive graphs | **Desmos API v1.9** | Key: `dcb31709b452b1cf9dc26972add0fda6` |
| Styling | **Manrope** font + custom CSS | Google Fonts |
| Hosting | **GitHub Pages** | GitHub Actions CI/CD |
| Video hosting | **GitHub Releases v1.0** | 23 Bilibili + 1 YouTube video |
| Video embed | **Bilibili iframe** + GitHub mirror toggle | |

## Project Structure

```
conics/
├── _quarto.yml              # Site config (D3 + Three.js in header)
├── styles.css               # All custom styles
├── video-toggle.html         # JS for video source toggle + fullscreen
├── CNAME                    # gaokao.moxin.app
├── CLAUDE.md                # This file
├── COURSE_REDESIGN.md       # Cognitive science design doc
├── gaokao_problems.md       # Raw gaokao problem bank
├── index.qmd                # EN+ZH index
├── index-zh.qmd             # ZH index
├── lessons.qmd              # Auto-listing
├── lessons/                 # 22 lesson .qmd files
│   ├── m1-01 to m1-04       # Module 1: 看见曲线 (visual intuition)
│   ├── m2-01 to m2-04       # Module 2: 掌握工具 (geometry→algebra)
│   ├── m3-01 to m3-05       # Module 3: 小题速解 (选择/填空)
│   ├── m4-01 to m4-06       # Module 4: 大题攻略 (解答题)
│   └── m5-01 to m5-03       # Module 5: 提分技巧 (optional)
├── images/ch01-ch22/        # Key frames (4 per video, 800px)
├── transcripts/             # Chinese ASR transcripts (.txt)
├── videos/                  # Downloaded MP4s (gitignored)
├── audio/                   # 16kHz mono WAV (gitignored)
└── .github/workflows/       # GitHub Actions deploy
```

## Course Design (Cognitive Science)

5 modules, 22 lessons, sequenced by cognitive difficulty:

| Module | Lessons | Principle | Goal |
|--------|---------|-----------|------|
| **1. 看见曲线** | 4 | CRA-Concrete | Pure visual, no formulas |
| **2. 掌握工具** | 4 | CRA-Abstract, dual coding | Derive formulas from geometry |
| **3. 小题速解** | 5 | Worked example + fading | 30-sec pattern recognition |
| **4. 大题攻略** | 6 | Sub-skill isolation | Systematic 解答题 workflow |
| **5. 提分技巧** | 3 | Expertise-appropriate | Optional advanced methods |

### Lesson Structure (every lesson follows this)

1. **探索问题** (Productive Failure) — think before learning
2. **几何直觉** (D3/Three.js animation) — see the math
3. **公式推导** (geometry → algebra) — understand the formula
4. **Worked Example** — study a complete solution
5. **Faded Example** — fill in missing steps
6. **Desmos探索** — free interactive exploration
7. **参考视频** — Bilibili embed with GitHub mirror toggle
8. **高考真题** — real exam problems with D3 curve diagrams
9. **速查表 + ⚠️易错点** — summary + common errors

## Content Pipeline

```
Bilibili video (yt-dlp)
  → ffmpeg: 16kHz mono WAV
  → mlx_whisper: Chinese transcription
  → ffmpeg + sips: 4 key frames per video (800px)
  → Claude: transcript → formal lesson .qmd
  → Claude: add Desmos + D3 + Three.js interactives
  → Claude: add gaokao problems with D3 diagrams
  → Quarto render → GitHub Pages
```

## Coding Conventions

### D3.js Animations
- Container: `<div class="d3-container" id="unique_id">`
- Controls: `<div class="d3-controls">` with buttons, sliders, value displays
- Always use **IIFE**: `(function() { ... })();`
- IDs prefixed by module: `m1_`, `m2_`, etc.
- Gaokao problem diagrams: `gaokao_mX_YY_qN` (200px height, static)
- Color scheme: red=foci, blue=curve, green=points, orange=distances, purple=special

### Three.js 3D Scenes
- Container: `<div class="three-container" id="unique_id">`
- Info overlay: `<div class="three-info">`
- Must use ES module imports:
  ```html
  <script type="module">
  import * as THREE from 'three';
  import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
  ```
- Always include OrbitControls for rotation/zoom
- Always include ResizeObserver for responsive sizing
- **Cone geometry:** Build parametrically with apex at origin, NOT using ConeGeometry (which offsets the apex)

### Desmos Graphs
- Container: `<div id="unique_id" class="desmos-container">`
- Load API once: `<script src="https://www.desmos.com/api/v1.9/calculator.js?apiKey=dcb31709b452b1cf9dc26972add0fda6">`
- API key: `dcb31709b452b1cf9dc26972add0fda6`

### Video Toggle
- Use `<div class="video-switch">` + `<div class="video-panel">` structure
- Buttons: `📺 B站原版` (default active) + `💾 GitHub镜像`
- For YouTube: `▶️ YouTube原版` + `💾 GitHub镜像`
- Videos hosted at: `https://github.com/ymote/conics/releases/download/v1.0/{filename}`

### Gaokao Problems
- Callout format: `::: {.callout-warning}` with `## 高考真题：20XX年 全国X卷 第X题`
- Collapsible solution: `::: {.callout-tip collapse="true"}` with `## 解题思路与答案`
- D3 diagram between problem text and solution
- 66 total problems, ~3 per lesson

### Language
- Primary: Chinese (formal, "我们" not "你")
- LaTeX for all math: `$...$` inline, `$$...$$` display
- Section headers in Chinese with English in parentheses where helpful
- No exclamation marks in prose

## Key Gotchas

1. **Three.js ConeGeometry** places apex at `y = height/2`, NOT at origin. Always build cones parametrically: `x² + z² = (y·tan(α))²`
2. **Dandelin sphere tangent circles** are at height `h·cos²(α)`, NOT at the sphere center height `h`
3. **Parabola parametrization** must satisfy `y² = 4fx`. Use `xOff = yOff²/(4f)`, NOT `xOff = t²·scale/2`
4. **Quarto `include-after-body: file:`** injects raw HTML — JS files need `<script>` tags wrapping
5. **Bilibili video filenames** get Chinese characters stripped on GitHub Releases — use the mangled names in URLs
6. **D3 inside `{=html}` blocks** — each script block needs its own IIFE to avoid variable conflicts

## Deployment

Push to `master` branch → GitHub Actions runs `quarto render` → deploys to GitHub Pages.

```bash
cd /Users/yuechen/home/sophie/conics
git add -A && git commit -m "description" && git push origin master
# Also mirror to mofa-org:
git push mofa master:main
```

Custom domain `gaokao.moxin.app` configured via CNAME file + GitHub Pages settings.

## Content Sources

| Creator | Platform | Followers | Content |
|---------|----------|-----------|---------|
| **一数 (Yi Shu)** | Bilibili | ~9M | 圆锥曲线二级结论, 技巧大全, 大题系列 |
| **赵礼显** | Bilibili | High | 焦点三角形内切圆 |
| **李永乐** | Bilibili/YouTube | Millions | 为什么叫圆锥曲线 (Dandelin球) |
| **3Blue1Brown** | YouTube | ~8M | Dandelin spheres proof |

## Related Projects

| Project | Repo | URL |
|---------|------|-----|
| 3B1B Calculus | `ymote/3b1b-calculus` | ymote.github.io/3b1b-calculus |
| Sophie's Calculus | `ymote/learningcalculus` | calculus.moxin.app |
| MathTube survey | `sophie/mathtube` (local) | — |
