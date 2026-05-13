# DJ Panel Pro - Web DJ Application

## Concept & Vision
A professional-grade DJ web application with realistic turntable interface, mixer controls, and audio effects. Users can mix tracks, apply effects, and create seamless transitions. Features member authentication via Supabase, track library management, and cloud sync.

## Design Language

### Aesthetic Direction
Dark, neon-accented interface inspired by professional DJ equipment (Pioneer CDJ/DJM series). Glowing elements, LED-style indicators, and tactile knob/slider designs.

### Color Palette
- **Background**: #0A0A0F (deep black)
- **Surface**: #1A1A24 (dark panel)
- **Primary**: #00FFFF (cyan glow - EQ/mid)
- **Secondary**: #FF00FF (magenta - bass/high)
- **Accent**: #FFFF00 (yellow - volume/levels)
- **Danger**: #FF3366 (peak/warning)
- **Success**: #00FF88 (play/active)
- **Text Primary**: #FFFFFF
- **Text Muted**: #888899

### Typography
- **Display**: Orbitron (futuristic DJ feel)
- **UI**: Inter (clean readability)
- **Monospace**: JetBrains Mono (timers/technical)

### Spatial System
- 8px base unit
- 16px standard gap
- 24px section spacing
- Border radius: 8px (panels), 50% (knobs)

### Motion Philosophy
- Smooth 200ms transitions for UI
- Realistic vinyl spin animation (CSS transform)
- LED pulse effects on beat
- Waveform scroll animation
- Knob rotation physics

## Layout & Structure

### Main DJ Interface
```
┌─────────────────────────────────────────────────────────────┐
│  HEADER: Logo | User Profile | Settings                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────┐      ┌─────────────────┐              │
│  │   DECK A        │      │   DECK B        │              │
│  │   Turntable     │      │   Turntable     │              │
│  │   Waveform      │      │   Waveform      │              │
│  │   Track Info    │      │   Track Info    │              │
│  └─────────────────┘      └─────────────────┘              │
│                                                             │
│  ┌─────────────────────────────────────────────────┐       │
│  │              MIXER SECTION                        │       │
│  │  [CH A] [CH B] | CROSSFADER | MASTER | EQ      │       │
│  │  Low/Mid/High knobs for each channel            │       │
│  └─────────────────────────────────────────────────┘       │
│                                                             │
│  ┌─────────────────────────────────────────────────┐     │
│  │  EFFECTS PANEL | BROWSER | SAMPLER               │     │
│  └─────────────────────────────────────────────────┘     │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  PLAYBACK CONTROLS: Play | Pause | Cue | Sync | Loop       │
└─────────────────────────────────────────────────────────────┘
```

### Responsive Strategy
- Desktop: Full dual-deck layout
- Tablet: Stacked decks with mixer
- Mobile: Single deck with mixer overlay

## Features & Interactions

### Core DJ Features
1. **Dual Deck System**
   - Two independent audio decks
   - Turntable visualization with vinyl animation
   - Pitch control (+/- 8%, +/- 16%, +/- 50%)
   - Beat sync button
   - Loop controls (1, 2, 4, 8 bar loops)

2. **Mixer Section**
   - Channel faders (A and B)
   - Crossfader with curve control
   - 3-band EQ (Kill switches)
   - Gain control per channel
   - VU meters with peak indicators

3. **Effects Panel**
   - Reverb
   - Delay/Echo
   - Flanger
   - Phaser
   - Filter (LP/HP/BP)

4. **Track Browser**
   - Search functionality
   - Sort by title/artist/duration
   - BPM display
   - Waveform preview

5. **Sampler/Hot Cues**
   - 8 sample pads
   - 4 hot cue points per track
   - One-shot and loop modes

### User System (Supabase Auth)
- Email/password registration
- Google OAuth login
- Profile management
- Track library per user
- Settings persistence

### Member Features
- Save playlists
- Track history
- Favorite tracks
- Cloud sync of settings
- DJ profile page

## Component Inventory

### Turntable Component
- **Default**: Vinyl disc with label, stationary
- **Playing**: Spinning animation (speed matches BPM)
- **Scratching**: Drag interaction, sound manipulation
- **Cueing**: Brief backspin effect

### Fader/Knob Components
- **Default**: Subtle glow outline
- **Hover**: Increased glow intensity
- **Active/Dragging**: Full glow, value display
- **Disabled**: Dimmed, no interaction

### VU Meter
- **Normal**: Green gradient
- **High**: Yellow gradient
- **Peak/Clipping**: Red flash, peak hold

### Track Card
- **Default**: Dark card with info
- **Hover**: Subtle lift, border glow
- **Playing**: Cyan border, animated icon
- **Queued**: Yellow indicator

### Playback Controls
- **Play**: Green glow when active
- **Cue**: Yellow, flash on tap
- **Sync**: Magenta, solid when synced

## Technical Approach

### Frontend
- Single HTML/CSS/JS (no framework for simplicity)
- Web Audio API for sound processing
- Canvas for waveforms
- CSS animations for visual effects

### Backend (Supabase)
- PostgreSQL database
- Supabase Auth for user management
- Row Level Security for user data
- Real-time subscriptions for shared sessions

### Database Schema
```sql
-- Users (handled by Supabase Auth)

-- Tracks
CREATE TABLE tracks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id),
  title TEXT NOT NULL,
  artist TEXT,
  duration INTEGER,
  bpm DECIMAL,
  audio_url TEXT,
  waveform_data JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Playlists
CREATE TABLE playlists (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id),
  name TEXT NOT NULL,
  track_ids UUID[],
  created_at TIMESTAMP DEFAULT NOW()
);

-- User Settings
CREATE TABLE user_settings (
  user_id UUID PRIMARY KEY REFERENCES auth.users(id),
  settings JSONB DEFAULT '{}',
  updated_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints (Supabase RPC)
- `get_user_tracks` - Fetch user's track library
- `add_track` - Add new track
- `delete_track` - Remove track
- `save_playlist` - Save playlist
- `get_settings` - Get user settings
- `update_settings` - Update settings

## Quality Requirements
- Audio latency < 50ms
- Smooth 60fps animations
- Responsive across devices
- Accessible controls
- Error handling for audio failures