# Hoyt Field — Full Build Prompt for Rabbit Hole Intern

> Paste everything below this line into the Rabbit Hole Intern.

---

Build me a complete, production-ready single-file web app called **Hoyt Field**. This is a mobile field companion app for a roofing and exterior construction company. It needs to run perfectly on the Rabbit R1's small screen (and any phone browser). Output ONE complete `index.html` file that contains all HTML, CSS, and JavaScript inline — no external files, no build step, no frameworks, no dependencies except Google Fonts loaded from CDN.

## WHAT THIS APP IS

Hoyt Field is a field-ready AI assistant for technicians at Hoyt Exteriors (Apple Valley, MN). It connects to "Wray," their private AI brain, via a REST gateway. The app lets field workers:
- Talk to Wray via voice (full duplex: push-to-talk + call mode)
- Chat via text with image attachments
- Capture and analyze inspection photos with Wray
- Play back training and inspection videos
- Manage and sync job tasks

## DESIGN SYSTEM — MATCH EXACTLY

```
Background:        #0A0A0A  (near black)
Surface cards:     #111111
Surface elevated:  #1A1A1A
Surface input:     #222222
Brand Red:         #C41E3A
Red dim:           #8B1528
Red glow:          rgba(196, 30, 58, 0.3)
White:             #FFFFFF
Gray primary:      #B8B8B8
Gray secondary:    #888888
Gray muted:        #555555
Border:            #2A2A2A
Success green:     #22C55E
Error red:         #EF4444

Fonts:
  Body:     Inter (400, 500, 600, 700, 800) — Google Fonts
  Brand:    Montserrat (700, 800) — Google Fonts

Design feel:
  - Premium dark mode, like a $50M company
  - Aether AI / high-end mobile app energy
  - Large touch targets (minimum 48px)
  - Smooth animations everywhere (200-300ms ease)
  - No clutter — every pixel earns its place
  - Optimized for one-handed use on a small screen
```

## APP STRUCTURE

### Fixed Header (always visible, 48px tall)
- Left: "HF" red square logo (28px, 7px radius, Montserrat 800) + "Hoyt Field" brand name (Montserrat 800, 16px)
- Right: connection status dot (green=connected, gray=offline, red=error) + gear icon button for settings

### Content Area (flex-1, fills between header and nav)
Five tabs that transition smoothly (opacity + translateY fade):
1. Voice
2. Chat
3. Camera
4. Videos
5. Tasks

### Bottom Navigation (60px + safe-area-inset-bottom)
- Surface background, top border
- Five tabs with SVG icons + labels
- Active tab: Brand Red color + 24px red accent line on top
- Inactive: #555555

---

## TAB 1: VOICE

This is the primary tab — the "face" of the app. Full-screen voice interface.

**Layout (centered column, padding 24px, gap 20px):**

1. **Animated Orb** (top, centered)
   - 3 concentric rings (140px, 160px, 180px) that pulse outward with staggered ripple animation when active
   - Core circle: 100px diameter, dark radial gradient, red border + red box-shadow glow when active
   - Inside core: microphone SVG icon (36px, gray when idle, red when active)
   - CSS class `voice-active` controls all animations

2. **Waveform** (12 bars, each 3px wide, red, animated up/down with staggered timing when active)

3. **Status text** — uppercase, 13px, gray — shows: Ready / Listening... / Thinking... / Speaking... / Call Active

4. **Transcript text** — 15px, italic, gray, centered — shows interim speech recognition result

5. **Response card** — dark surface card (16px padding, border, 16px radius) — shows Wray's last response. Empty state: placeholder text in muted gray.

6. **Controls row** — three items horizontally centered:
   - Left: "Call Mode" pill button (icon + label, toggles active state with red background tint)
   - Center: Large circular PTT button (80px, red, white mic icon, hold to talk, release to send)
   - Right: "Clear" pill button (trash icon + label)

**PTT behavior:**
- touchstart/mousedown: start speech recognition, show interim results in transcript
- touchend/mouseup/mouseleave: stop recognition, send transcript to Wray, stream response into card, speak it via TTS
- Show animated orb + waveform + "Listening..." during recognition
- Show "Thinking..." while waiting for Wray
- Show "Speaking..." and animate orb while TTS plays

**Call Mode:**
- Toggle continuous: after Wray finishes speaking, auto-restart listening so it feels like a phone call
- Show "Call Active" in status when on

---

## TAB 2: CHAT

Classic chat interface.

**Layout:**
- Top: scrollable messages list (flex-1, overflow-y auto)
- Bottom: fixed input bar (surface background, top border)

**Messages:**
- User messages: right-aligned, brand red bubble, white text, bottom-right radius 4px, small avatar (red circle, "T" for tech or "L" for Levi)
- Wray messages: left-aligned, dark surface bubble, border, bottom-left radius 4px, "W" avatar in dark circle
- Fade-in animation on new messages
- Streaming: Wray messages stream in chunk by chunk (update in-place, no flash)
- Typing indicator: three bouncing dots in a dark bubble while waiting for first chunk
- Image preview in bubble (200px max-width, rounded) when message includes an image
- Auto-scroll to bottom on new messages

**Input bar (left to right):**
- Camera/attach icon button (38px circle, dark surface)
- Textarea (flex-1, 20px radius, dark surface, auto-grow to max 100px, Inter font)
- Voice mic button (38px circle, dark — turns red "recording" state when active)
- Send button (38px circle, brand red)

**Chat voice input:**
- Tap mic button to start recording (button turns red)
- Speech fills textarea in real-time
- Auto-stop on final result (button returns to normal)
- Textarea contents send normally via send button

**Image attach:**
- Tap camera icon → file input for image pick
- Toast confirms attachment
- Image attached to next send

---

## TAB 3: CAMERA

Live camera view with inspection capture and analysis.

**Layout (full height flex column):**
- Camera view (flex-1): `<video>` element, object-fit cover
- Analysis result panel (max-height 140px, overflow-y auto) — shown only after analysis

**Camera overlay (bottom, gradient to black):**
- Left button: "Retake" (circular frosted glass, hidden until capture) 
- Left button: "Flip" camera icon (always visible when live)
- Center: Large shutter button (72px white circle with inner circle) — tap to capture
- Right button: "Analyze" (magnifying glass, shown after capture)
- Right button: "Send to Chat" (paper plane, shown after capture)

**Behavior:**
- Auto-starts camera when tab is opened, stops when leaving
- Capture: draw video frame to canvas, display as overlay image, show retake/analyze/send buttons
- Analyze: send base64 image to Wray with prompt "Analyze this field inspection image. Identify damage, issues, or notable observations. Be specific and actionable." — stream result into analysis panel, speak it via TTS
- Send to Chat: switch to Chat tab, attach image data, show toast "Image ready"
- Retake: hide capture overlay, return to live preview, hide capture buttons
- Camera not available: show centered error message with camera-slash icon

---

## TAB 4: VIDEOS

Video playback for training and inspection footage.

**Layout (flex column, full height):**
- HTML5 video player (top, full width, 16:9 aspect, black background, native controls)
- URL bar (surface, border-bottom, flex row): text input "Paste video URL..." + "Play" button (red) + upload icon button
- Video list (flex-1, overflow-y auto)

**Video list items:**
- Film icon thumbnail (dark card, 56x40px)
- Name + meta (type, date added)
- Tap to load into player and play
- Empty state: centered icon + "No videos yet"
- Store in localStorage (max 20 entries)

**Adding videos:**
- URL: type/paste URL → Play → loads into player, adds to list
- File: tap upload button → file picker (video/*) → loads into player (object URL), adds to list

---

## TAB 5: TASKS

Job task management with Wray sync.

**Layout:**
- Header row: "Tasks" title + progress bar (80px) + "X/Y" counter
- Actions row: "Sync from Wray" button + "Clear done" button
- Add task bar: text input "Add a task..." + "Add" button (red)
- Task list (scrollable)

**Task items:**
- Checkbox (22px, 6px radius, red fill + white checkmark when done)
- Task text (strikethrough + muted when done)
- Delete button (× icon, only visible on hover/tap)
- Tap checkbox to toggle complete

**Sync from Wray:**
- Sends message to Wray: "List my current tasks and job priorities as a simple numbered list. Just the tasks, no commentary."
- Parse numbered list from response
- Add non-duplicate tasks to list
- Toast "N tasks synced from Wray"

**Persist:** save to localStorage on every change

---

## SETTINGS OVERLAY

Bottom sheet (slides up from bottom, rest is blurred backdrop).

**Fields:**
- Wray Gateway URL (text input, default: `http://159.65.33.45:18789`)
- Wray Gateway Token (password input, default: `clawx-21183e9e88e165a362bc3f41491ff4b5`)
- Your Role (select: Field Technician, John (Service Manager), Jonny (Project Manager), Levi (Owner), Paul, Lisa (Office))
- Voice Output TTS toggle (default ON)
- Auto-Listen after response toggle (default OFF)

**Buttons:**
- "Save Settings" (full-width red primary)
- "Test Wray Connection" (full-width secondary)

**Test connection:** try `GET /health` on the gateway, fallback to a minimal chat completions call. Show toast result.

All settings persist to localStorage.

---

## WRAY API CONNECTION

```
Endpoint:  {wrayUrl}/v1/chat/completions
Method:    POST
Headers:
  Content-Type: application/json
  Authorization: Bearer {wrayToken}
  x-openclaw-session-key: agent:main:field:{role}
  x-openclaw-message-channel: field

Body:
{
  "model": "openclaw",
  "messages": [ ...systemPrompt + history + userMessage ],
  "stream": true   // for chat/voice
}

Streaming: Server-Sent Events (SSE)
  Each line: "data: {json}"
  json.choices[0].delta.content = chunk
  Final line: "data: [DONE]"

Non-streaming (for camera analysis):
  stream: false
  Response: json.choices[0].message.content
```

**Conversation history:**
- Keep last 20 messages (10 turns) in memory array
- Reset on "Clear" button
- Each successful send/receive appends to history
- Shared between Voice and Chat tabs

**Error handling:**
- Show toast on connection failure
- Set status dot to red
- Don't crash, show error in response area

---

## SYSTEM PROMPT (inject as first message in every request)

```
You are Wray, the AI brain for Hoyt Exteriors — a family-owned exterior construction company in Apple Valley, MN. Est. 2000. "The company your dad tells you to call."

You're speaking with a field technician through the Hoyt Field app on a Rabbit R1 device. Be concise, direct, and confident. Technicians are on roofs and in the field — no essays, no fluff.

CORE EXPERTISE:
Roofing: Assess asphalt (3-tab 15-20yr, architectural 20-30yr), metal (40-70yr), flat TPO/EPDM. Look for missing shingles, curling, blistering, granule loss, lifted tabs, moss, algae, flashing failure (chimney, vent, valley, wall). Ice dams = attic ventilation issue, NOT gutters.

Siding: LP SmartSide focus. Check gaps (3/16" at joints, 1" above grade), fasteners (12-16" OC), caulk failure, water damage, thermal expansion.

Gutters: Proper slope 1/4" per 10 feet. Downspouts extend 5ft+ from foundation. Hangers 24" OC max. Ice dams are NOT a gutter problem — they are an attic ventilation problem.

Decks: Minnesota frost line is 42 inches — ALL footings must be 42" minimum or they WILL frost heave. Ledger connection = most critical, rot here = structural safety hazard. Railing: 36" min height, 4" max baluster spacing.

Concrete: Polyurea sealer best for MN freeze-thaw (5-7yr life).

MINNESOTA CRITICAL:
- Frost line: 42 inches (absolute rule for deck footings)
- Freeze-thaw = #1 damage driver everywhere
- Stainless steel fasteners required
- Painting window: 50-85°F only

QUALITY STANDARD (Hoyt Premium):
All fasteners flush, all gaps caulked color-matched, straight/level alignment, clean jobsite.

ESCALATE IMMEDIATELY (tell tech to stop and call Levi):
- Roof sagging or structural damage
- Ledger rot on decks (house structural risk)
- Extensive mold inside
- Any immediate homeowner safety risk

Keep responses short and field-ready. When analyzing images, be specific about what you see. Give the tech a clear next step.
```

---

## VOICE ENGINE

Use Web Speech API (`window.SpeechRecognition || window.webkitSpeechRecognition`).

```javascript
// Speech Recognition
recognition.continuous = false
recognition.interimResults = true
recognition.lang = 'en-US'

// onresult: show interim in transcript, capture final
// onerror: handle gracefully, don't crash
// onend: update UI state

// Speech Synthesis
const utter = new SpeechSynthesisUtterance(cleanedText)
utter.rate = 1.05
utter.pitch = 1.0
// Prefer: Google en-US voice if available
// Strip markdown before speaking: **bold**, *italic*, # headers, ` backtick `

// Full duplex feel:
// - Stop TTS when PTT pressed (speechSynthesis.cancel())
// - Resume listening after TTS ends in call mode
// - Show correct status text throughout
```

If Web Speech API not available: show toast "Voice not supported in this browser" and disable voice buttons gracefully. Text input always works.

---

## ANIMATIONS

```css
/* Orb rings ripple outward (3 staggered) */
@keyframes ripple {
  0% { transform: scale(0.8); opacity: 0.5; }
  100% { transform: scale(1.2); opacity: 0; }
}
/* Delays: 0s, 0.4s, 0.8s */

/* Waveform bars (12 bars, staggered peaks) */
@keyframes wave {
  0%, 100% { height: 4px; }
  50% { height: 4-28px depending on bar; }
}

/* Message fade in */
@keyframes msgIn {
  from { opacity: 0; transform: translateY(6px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Typing dots bounce */
@keyframes typingDot {
  0%, 60%, 100% { transform: translateY(0); opacity: 0.4; }
  30% { transform: translateY(-6px); opacity: 1; }
}
/* Delays: 0s, 0.2s, 0.4s */

/* Tab transitions: opacity + translateY(8px→0) */
/* All transitions: 200-300ms ease */
```

---

## PWA SETUP

- Viewport: `width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover`
- Theme color meta: `#0A0A0A`
- Apple web app capable meta tags
- Generate PWA manifest inline via JavaScript (Blob URL, inject as link tag)
- `-webkit-tap-highlight-color: transparent` on everything

---

## TOAST NOTIFICATIONS

- Fixed position above bottom nav
- Dark surface, border, 24px radius pill
- Fade in/out (opacity + translateY)
- Auto-dismiss after 2.2 seconds

---

## DATA PERSISTENCE (localStorage keys)

```
wray_url          Gateway URL
wray_token        Gateway token
wray_role         User role
wray_voice        TTS enabled (boolean string)
wray_autoListen   Auto-listen enabled (boolean string)
hf_tasks          JSON array of task objects {text, done}
hf_videos         JSON array of video entries {name, url, type, added}
```

---

## COMPLETE FEATURE CHECKLIST

- [ ] 5-tab bottom navigation with active state
- [ ] Voice tab: animated orb, waveform, PTT button, call mode, status text, response card, clear button
- [ ] Full duplex voice: STT → Wray → TTS → auto-listen if call mode
- [ ] Chat tab: streaming message bubbles, typing indicator, text input, voice input, image attach
- [ ] Camera tab: live preview, capture, analyze with Wray, send to chat, flip camera, retake
- [ ] Video tab: HTML5 player, URL input, file upload, persistent video list
- [ ] Tasks tab: add/complete/delete tasks, sync from Wray, progress bar, persist to localStorage
- [ ] Settings overlay: all config, save to localStorage, test connection
- [ ] Connection status dot in header
- [ ] Toast notification system
- [ ] Graceful error handling everywhere (no crashes, always shows error in UI)
- [ ] Markdown stripped from TTS output
- [ ] PWA manifest injected
- [ ] Conversation history (last 20 messages) shared between Voice and Chat
- [ ] Responsive to any small screen, optimized for 360px wide portrait
- [ ] No external dependencies except Google Fonts

---

## OUTPUT

Output the complete `index.html` file. Everything inline. No separate files. Make it work on first load with no configuration needed (defaults are pre-filled). The design should look and feel premium — "the company your dad tells you to call." Dark, clean, confident, built for the field.

Brand line: **Hoyt Exteriors · Apple Valley, MN · Est. 2000 · "The company your dad tells you to call."**
