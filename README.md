# ASO App Store & Google Play Screenshots

A Claude Code skill that generates high-converting App Store and Google Play screenshots for your app. It analyzes your codebase, identifies core benefits, and creates professional screenshot images using AI.

Supports **iPhone**, **iPad**, and **Android** device frames.

## What It Does

1. **Benefit Discovery** — Analyzes your app's codebase to identify the 3-5 core benefits that drive downloads
2. **Screenshot Pairing** — Reviews your simulator/emulator screenshots, rates them, and pairs each with the best benefit
3. **Generation** — Creates polished store screenshots using a two-stage process: deterministic scaffolding (compose.py) + AI enhancement (Nano Banana Pro via Gemini MCP)
4. **Showcase** — Generates a preview image with all screenshots side-by-side

## Installation

### 1. Add the skill to Claude Code

```bash
claude install-skill github.com/Skipperlla/claude-skill-aso-appstore-screenshots
```

### 2. Install Python dependencies

```bash
pip install Pillow
```

### 3. Font requirement

The skill uses **SF Pro Display Black** for headline text. On macOS, install it from [Apple's developer fonts](https://developer.apple.com/fonts/). The expected path is:

```
/Library/Fonts/SF-Pro-Display-Black.otf
```

### 4. Set up Gemini MCP (for AI enhancement)

The generation phase requires [@houtini/gemini-mcp](https://www.npmjs.com/package/@houtini/gemini-mcp) to be configured as an MCP server in Claude Code:

```bash
npm install -g @houtini/gemini-mcp
```

Then add it to your Claude Code MCP config (`~/.claude/settings.json` or project `.mcp.json`).

## Usage

From within your app's project directory, run:

```
/aso-appstore-screenshots
```

The skill will guide you through each phase interactively. Progress is saved to Claude Code's memory system, so you can resume across conversations.

## Supported Devices

| Platform | Device | Dimensions | `--device` flag |
|----------|--------|------------|-----------------|
| iOS | iPhone 6.5" | 1242 × 2688 | `iphone-6.5` |
| iOS | iPhone 6.7" | 1290 × 2796 | `iphone-6.7` (default) |
| iOS | iPhone 6.9" | 1320 × 2868 | `iphone-6.9` |
| iOS | iPad Pro 12.9" | 2048 × 2732 | `ipad-12.9` |
| iOS | iPad Pro 11" | 1668 × 2388 | `ipad-11` |
| Android | Phone | 1080 × 2400 | `android` |

## How It Works

### Scaffold → Enhance Pipeline

Rather than generating screenshots from scratch (which produces inconsistent results), the skill uses a two-stage approach:

1. **compose.py** creates a deterministic scaffold with exact text positioning, device frame, and your screenshot composited inside
2. **Nano Banana Pro** (via Gemini MCP) enhances the scaffold — adding a photorealistic device frame, breakout elements, and visual polish

This ensures consistent layout across all screenshots while letting AI handle the creative enhancement.

### Batch Generation

Generate screenshots for multiple platforms in one command:

```bash
# Single device
python3 compose.py --bg "#E31837" --verb "TRACK" --desc "CARD PRICES" \
  --screenshot shot.png --output out.png --device iphone-6.7

# Multiple devices (comma-separated)
python3 compose.py --bg "#E31837" --verb "TRACK" --desc "CARD PRICES" \
  --screenshot shot.png --output out.png --device "iphone-6.7,android,ipad-12.9"

# All devices at once
python3 compose.py --bg "#E31837" --verb "TRACK" --desc "CARD PRICES" \
  --screenshot shot.png --output out.png --device all
```

When using multiple devices, output files are named `out-iphone-6.7.png`, `out-android.png`, etc.

### Output

Screenshots are saved to a `screenshots/` directory in your project:

```
screenshots/
  01-benefit-slug/          ← working versions
    scaffold.png            ← deterministic compose.py output
    v1.jpg, v2.jpg, v3.jpg  ← AI-enhanced versions
    v1-resized.jpg, ...     ← cropped to store dimensions
  final/                    ← approved screenshots, ready to upload
    01-benefit-slug.jpg
    02-benefit-slug.jpg
  showcase.png              ← preview image with all screenshots
```

The `final/` folder contains store-ready screenshots at exact dimensions for the target platform.

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | The skill prompt — defines the multi-phase workflow |
| `compose.py` | Deterministic scaffold generator (Pillow-based) |
| `generate_frame.py` | Generates device frame templates (iPhone, iPad, Android) |
| `showcase.py` | Generates the side-by-side showcase image |
| `assets/device_frame.png` | Pre-rendered iPhone device frame template |
| `assets/device_frame_android.png` | Pre-rendered Android device frame template |
| `assets/device_frame_ipad.png` | Pre-rendered iPad device frame template |

## License

MIT
