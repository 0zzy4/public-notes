Breadcrumbs: [[CS 6457]]
## Overview

**Release:** 1998
**Developer:** DreamWorks Interactive
**Producer:** Seamus Blackley
**Genre:** First-person action/exploration with combat
**Commercial Success:** Disappointing, but highly influential

**Key Theme:** Extremely ambitious project that pushed hardware and player expectations beyond what was feasible at the time

## DreamWorks Interactive History

**Founding Concept:**
- Entertainment "trifecta": movies, music, and video games
- Founded by Spielberg, Katzenberg, and Geffen
- Goal: Attack consumer media market from all angles
- Intended as challenge to Disney
- Ultimately unsuccessful in original vision
- DreamWorks brand survived, focused on animation

## Seamus Blackley Background

**Expertise:** Physics expert, studied at MIT

**Previous Work:**
- Worked at Looking Glass Studios (no longer exists)
	- **Ultima Underworld:** One of first true 3D first-person games, particle dynamics for arrows/slings
	- **Flight Unlimited:** Real-time fluid dynamics for air movement over simulated wing (considered best flight sim controls for years)
	- **System Shock:** Sci-fi first-person shooter
- Xbox evangelist (helped develop and promote Microsoft's game console)
- Creative Artists Agency rep
- Active on Twitter discussing old game development

## Development Issues

**Major Problem:** Development extended too long, DreamWorks forced completion before ready
- Many loose ends left unresolved
- Physics didn't achieve stability goals
- AI had to be gutted

## Interface Design (VR-like Controls)

**Philosophy:**
- **No heads-up display (HUD)**
- Everything contextual in environment
- No abstract interactions (button presses, running over collectibles)
- Had to manipulate character's arm to interact with objects

**Health System:**
- Tattoo (heart outline) on character's chest
- Look down to see red ink indicating damage
- Red ink disappears as health recovers
- Character (voiced by Minnie Driver) speaks status

**Ammo System:**
- Character verbally announces ammo count
- Example: "About three left" after firing

**Weapon Aiming:**
- No reticle
- Must use iron sights (like real weapons)
- Align two metal pieces (one near eye, one near barrel)
- Very challenging in video game context

**"VR" Controls:**
- "Realistic" and cumbersome inverse kinematics arm manipulation
	- Mouse controls 2D hand position relative to camera
	- Elbow bends and follows automatically
	- Modifier keys for wrist bending, grabbing, throwing
	- Hold gun in front of body, no crosshair, gun has recoil
	- Very challenging and confusing for average players

**Assessment:**
- Interesting idea that would work better in VR
- Possibly suited for Nintendo Wii
- Made controls very challenging for 1998 players

## Graphics Technology
### Software Renderer

**No 3D Accelerator then:**
- Used software rendering (CPU-based)
- Required creative algorithms for performance
- Could run at higher resolutions with modern patches

### Dynamic Billboarding (Trees/Objects)

**Technique:**
- Off-screen objects rendered to texture buffer
- Trees rendered as 3D models to off-screen buffer
- Displayed as 2D billboards in scene
- Closest tree remains 3D model (~100 polygons)
- Distant trees are billboards

**Dynamic Updates:**
- Error metric determines when to recalculate
- Billboard recalculates when viewing angle changes too much
- Tree "snaps" visible when recalculating
- Trees switch from billboard to model as player approaches

**Benefits:**
- Allowed complicated rolling terrain
- Impressive visual quality for the time
- Efficient rendering of jungle environments

## AI System (Broken)

**Original Vision:**
- State machine-based AI
- Dinosaurs work together (raptor flanking attacks)
- Dinosaurs have changing moods/desires (hunger, satisfaction)
- Strategy: Feed T-Rex herbivore to distract from player

**Problems:**
- State machines get complicated very quickly
- Difficult to extend with new states/behaviors
- Each addition complicates entire implementation

**Final Result:**
- Had to disable all personality aspects
- Dinosaurs attack player on sight (exactly what they didn't want)
- Exception: Some herbivores leave player alone unless bothered
- Dinosaurs programmed to never enter buildings (AI too buggy)

## Level Editor Problems (3D Studio Max)

**Initial Decision:**
- Use 3D Studio Max plugin architecture
- Extend modeling software for level building
- Seemed better than building custom tools

**Critical Bug:**
- Metadata extensions stored game-specific data
- Buffer overflow when extensions got too large
- Undocumented size limits
- Silent corruption: geometry appeared/disappeared randomly
	- due to extensions being too large, which caused buffer to overflow
- Lost significant work

**Solution (Kludge):**
- Empirically determined maximum metadata size
- Daisy-chained child objects for additional storage
- Each child provided more metadata space
- Worked but wasn't best solution

**Lesson:** Should have built their own tools instead

## Physics Engine
### Implementation

**Bounding Box System:**
- Everything enclosed/defined by boxes
- Compound objects: multiple boxes joined together
- Good strategy: highly optimized collision detection
- Focused on single primitive type

**Penalty Force Method:**
- Only option given 1998 processor capabilities
- Corrective force applied to deepest penetration point only
- No skin offset
- No multiple contact points
- No iterative solving for simultaneous correction

### Positive Features

**Inverse Kinematics:**
- Coupled to physics system
- Dinosaurs have ragdoll body at all times
- Can bump into player and objects
- Tails knock down obstacles

**Ragdoll Physics:**
- First game to implement ragdoll (pioneering)
- Dead dinosaurs enter ragdoll state
- Can roll down hills realistically
- Some spring system wobbling visible

### Limitations

**Air Hockey Effect:**
- Objects oscillate around center of mass
- Rarely touch ground (vibrate constantly)
- Poor friction modeling
- Objects slide unrealistically
- Cannot stack crates (slip off each other)
- Dinosaurs moved weirdly

**Object Interpenetration:**
- Fast-moving objects can get stuck
- Different parts want to be on opposite sides of wall
- Fighting back and forth between sides
- Example: Gun stuck in wall, never recovered

**Impact on Gameplay:**
- Originally intended constructive puzzles (stacking, bridges, staircases)
- Had to cut nearly every puzzle requiring stacking
- Physics only reliable for destructive gameplay (knocking things over)
- Level designers had already built levels assuming stable physics
- Extremely frustrating for players who tried to stack

## Sound Design (Real-time Foley Effects)

**Concept:** Digital version of traditional Foley artists

**Traditional Foley:**
- Sound effects artists create sounds with props
	- Coconut shells = horse hooves on cobblestones
	- Dropping glass = broken glass sounds
	- Metal sheets = thunder
- Originated in live radio dramas

**Trespasser Implementation:**

Database System:
- Collision callbacks trigger sound lookup
- Material properties determine sound (metal on stone, etc.)
- Multiple versions of each sound type (3-4 variations)
- Random selection for variety

Dynamic Characteristics:
- Energy mapping: mass × speed = volume
- Higher energy = louder sounds
- Lower energy = softer sounds
- Pitch shifting for additional variety

One of coolest contributions, impressive audio system

## Graphics: 3D Acceleration Problem (Biggest Issue)

**Situation:**
- High system requirements at launch
- Gamers just got 3DFX Voodoo graphics cards
- Voodoo provided amazing performance for Quake
- Customers expected all games to support 3DFX

**Voodoo Limitations:**
- Very primitive capabilities
- Only bilinear texture filtering
- Limited configuration
- No off-screen rendering
- No custom buffers
- No custom shader passes
- Fixed lighting only

**Trespasser's Advanced Needs:**
- Bump mapping
- Image caching for dynamic billboards
- Extra lighting layers
- Shadow mapping
- All impossible on Voodoo hardware

**Outcome:**
- Team wrote Voodoo renderer anyway
- Killed many cool features
- Most customers only tried Voodoo version
- Software renderer actually looked and played better
- Game designed for software renderer
- This issue, on top of everything else, doomed the game

## Virtual Texturing (Advanced Feature)

**Concept:** Virtual memory applied to texture management

**How It Works:**
- Similar to OS virtual memory for processes
- Geometry refers to virtual texture memory
- Textures swapped based on what's visible
- Don't need castle textures when in dungeon
- Don't need dungeon textures when at castle

**Mipmaps:**
- Multiple resolution levels of same texture
- Lower resolution for distant objects
- Reduces aliasing (ants marching effect)
- Can use lower resolution when bandwidth limited

**Graceful Degradation:**
- Memory manager tries to load correct textures
- Falls back to lower mipmap levels if needed
- Catches up when memory bandwidth available

**Similarity to Megatexturing:**
- John Carmack used similar concept in later Quake games
- Best known in game Rage
- Massive texture space with virtual management
- Trespasser had conceptually similar system first

---
## Technology Contributions Summary

**Pioneering Features:**
- Inverse kinematics with physics
- Ragdoll physics (first game)
- Real-time Foley sound effects
- Dynamic billboarding for trees/objects
- Virtual texturing system
- Ambitious physics engine for 1998

**Lessons Learned:**
- Importance of playtesting early in development
- Technology expectations must fit gameplay
- Overly ambitious projects risk failure
- Need to balance innovation with feasibility
- Custom tools often better than extending third-party software

**Legacy:** Highly influential despite commercial failure, pioneered many technologies used in modern games