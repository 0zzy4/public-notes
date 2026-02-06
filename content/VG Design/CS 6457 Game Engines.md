Breadcrumbs: [[CS 6457]]
## Introduction
### Early Game Engine
- Computation kernel that runs the game
	- optimized continuous frame based loops
	- interprets user input & update simulation & render to device
	- ROM that tells you the pixels
- Operates on internal data models
- Some level of reusability (at least within the game, e.g. levels)
#### The Kernel
- Frame-based
- Closed-loop where player is part of the overall system
- Loop:
	- Process user input
	- Update the state of the simulation
	- Render - output to display device
	- Sleep/Sync
	- repeat
### Human Perception
- Visual stimulus reaction: 0.2s
- Auditory Stimulus Reaction: 0.16s
#### Fool the Senses
- Sensory Illusions: vision, hearing, others?
Goals
- Object Representation
	- Permanence
- Relationships
	- Spatial Constraints (object on top of another)
- Cause & Effect
- Real world representation
	- objects, physics
- Immersive simulation
### Animation
- Consecutive **similar** images appear to be persist shapes that move/change if image change rate is "fast enough"
- 10 FPS enough for sense of spatial presence
	- Movies: standardized on 24 FPS
	- Games: aim for >= 30 FPS

---
## Simulation Concepts
### Models of Human Observable Macroscopic Phenomenon

**Pursuit of Realism:** Goal is to create increasingly realistic simulations. Can still include fantasy elements, but audiences have expectations for how things should look, sound, and interact based on real-world experience.

**Two Approaches to Simulation:**

Models of Human Observable Macroscopic Phenomenon:

- Often disparate models that must be synchronized:
	- surface rendering (how things look)
	- digital audio (how things sound)
	- Newtonian physics (rigid body dynamics, collision, friction, rolling)
	- animation systems
	- light interaction (how light bounces off surfaces)
- Limited interaction of elements within the simulation
	- aka hard to integrate the disparate models bc they're separate, independent models
- Focus on illusions - computation dedicated only to observable phenomenon
- Advantage: Storytellers have more direct control of consequences

Unified Simulation:

- Emergent observable phenomenon (results emerge from fundamental rules rather than being directly programmed)
- High computational costs
- Challenge: There is no Theory of Everything (yet)
- Limitation: Would be difficult for a storyteller to control or constrain outcomes

So Basically:
- **Unified** = simulate reality and see what happens
- **Disparate models** = make it look/sound/feel like reality while maintaining creative control
Key Tradeoff: Direct artistic control vs. emergent realism

### Twilight Zone!
*The Twilight Zone* is a science fiction tv series. The episode "A Matter of Minutes" explores the concept of time and reality. It plays with the idea that our reality is composed of artificial worlds, or simulated worlds, that are created for each discrete moment in time. The plot is about a couple who gets stuck in a particular moment, minute 11:37 am

The premise of the episode if very similar to how video games work, at least in terms of a computation kernel.

So what did the episode get right about the simulation concepts?
- They described reality as a series of simulated time slices
- Each time slice, or frame, has to be rebuilt
	- In the episode, there are humanoid beings, called "Time Builders", that construct the world for each minute
- Build for the perspective of the observer (person living in the world)
	- The Time Builders only build the parts of reality that the observer will see
	- reduces waste of resources
- Glitch out of game world
	- The entire premise of the episode is the couple seeing this 4th dimension, basically glitching out of the world, seeing outside of the rendered area
- Objects disappearing or popping up
	- Time Builder explained to the couple that when people "misplace" items like their keys, this is actually mistakes from the Time Builders
- Concept of a buffer
	- The Time Builders begin designing in advance, for future time slices
### Revisit Concept of: A frame frozen in time
- Almost everything in a frame update shares a common reference time (from the start of frame processing)
- Visual rebuilt from scratch
- Only simulate what is needed to create the illusion
	- computationally expensive to simulate everything
#### Why is frame time fixed/frozen?
- Consistent output (especially visually) as all game objects animate by the same amount of time
- Potentially avoid race conditions in logic that dictates object interactions
### Only simulate what is needed
- Surface rendering
	- Don't care about what's underneath since most of the light we see is reflecting off the surface of the material
- Frustum and occlusion culling
	- Can ignore anything outside the camera frustum
	- If object is behind another (z-buffer), then don't include it
	- ![[Pasted image 20260112103949.png]]
#### Side Effects of Detail Management
Because we want to optimize (e.g. rendering only what's seen) to keep frame rates high & keep the simulation interactive, we may make mistakes.

May not be enough processing power, leading to breaking the illusion

Examples
- Pop-in/Pop-out of objects and characters
- Inactivated characters
- Characters teleport when you aren't looking/out of range
- Glitch out of levels
- Disappearing objects
- See inside solids
### Frame Rate Considerations
**Minimum Thresholds:**
- Bare minimum for animation: 10 frames per second
- Below 30 fps: game doesn't look great, computer seems slow
- Most people can notice differences up to 60 fps for synthesized animation

**Two Scenarios for Frame Rate:**
Passive Observation (watching TV, YouTube videos):
- Only perceiving the quality of animation
- Can typically notice differences up to 60 fps
- Beyond 60 fps, most people can't detect improvement

Interactive Animation (video games):
- Perceiving both animation quality AND input-response latency
- Round trip time matters: joystick deflection/mouse movement → seeing response on screen
- Can notice performance differences up to ~120 fps due to input lag perception
- Higher frame rates reduce perceived latency between action and response
### Why Frame-Based Simulations?
#### Reasons for Frame-Based Approach:
Intuitive and Easy to Understand:
- Conceptual simplicity: tight loop that rapidly updates, creating one image after another
- Easy to build upon: can add different macroscopic simulation phenomena by adding components to the game engine

Built on Established Knowledge:
- Geometric methods for describing visuals
- Historical entrenchment: classic celluloid animation (camera-captured films, hand-drawn Disney movies)
- Digital signal processing and sampling theory fit well with frame-based approach
- Raster displays with individual pixels
- Evolution from passive animation to interactive animation while maintaining frame concept

Compatible with Human Perception:
- Show similar images fast enough and the brain perceives continuous motion rather than a slideshow
- Effective at creating the illusion of movement
- All we need is something easy to work with that successfully tricks the user
#### Audio in Frame-Based Systems:
Audio doesn't actually use frames - it's presented continuously:
- Audio runs in parallel as a continuous stream
- Uses a circular buffer (small buffer holding 1-2 frames worth of audio in terms of time)
- Buffer is constantly "topped off" frame-to-frame with appropriate audio data
- Events that generate audio (e.g., physics collision) are coupled to frame-based simulation
- As long as sufficient frame rate is maintained, audio plays continuously
- Audio can only be updated at the frame rate and incurs latency related to buffer size

### Canonical Render Pipeline (GPU):
Modern graphics rendering is highly parallelized:
- GPU is a dedicated processor for rendering graphics
- Virtual camera concept: determines what's visible from a particular perspective (first-person, third-person, etc.)
- Once frame timestamp and pose (camera/object positions in 3D space) are identified, that snapshot can be rendered
- Work is split up and parallelized across many render units
- Extremely high throughput and efficiency allows for huge amounts of detail
- Any alternative approach must compete with this highly optimized system
#### Z-Buffer for Synchronization:
Solves the problem of determining which surfaces appear in front of others:
- Each pixel stores RGB values PLUS a depth value (distance from camera)
- Allows parallel render units to coordinate
- Only writes a pixel if it's closer than what was previously rendered
- Operation must be atomic: check (if new depth < old depth) and write (new RGB + depth) happen together as one operation
- Simple concept that enables highly parallelized rendering

### Alternatives to Frame-Based Simulation

**Why Consider Alternatives?**
Worth examining to better understand why we use frame-based simulation in the first place.
#### Ray Tracing as Alternative Rendering

**Traditional Ray Tracing Approach:**
- Ray tracing can be locked to a frame rate: render every pixel with sufficient ray casts to capture entire frame buffer
- Recently becoming commercially viable for video games in the last couple years

**"Frameless" Ray Tracing (Best Effort Approach):**
- Ray tracing process gets a certain amount of time
- If not done by the time a new image needs to be sent to screen, send reduced number of updated pixels
- Result: mixture of new and old pixels from different generations
- Visual artifacts: speckled patterns because some pixels update with rays and some don't
- If camera stops moving, everything visually synchronizes; movement shows the changes

**Challenges:**
- Not completely frameless, but demonstrates what breaking from traditional frames looks like
- Would need very high update rate to minimize speckled artifacts
- Each pixel would need to be updated individually at a very high average rate

#### Particle-Based Rendering

**Moving Away from Surface-Based Rendering:**
- Rather than disparate approaches welding together isolated simulations, use something more fundamental and closer to reality
- Instead of polygon mesh surfaces, use particles (like atoms)

**"Jelly in the Sky" Example (2D particle-based game engine):**
- Each pixel roughly corresponds to a particle in the simulation
- Particles form solids by creating bonds with neighboring particles (unless too hot)
- Weapons in the tank battle game add energy/heat to materials
- When melted, bonds break and materials flow like liquids
- Materials cool off based on simple thermal model

**Technical Challenges:**
- Issues with floating-point accuracy and numerical concerns make it difficult to create structurally strong solids
- Side effect: objects act more like jelly (developer embraced this as game design concept)
- Models interior of objects, not just surfaces/edges
- Every particle has physical simulation

**Scaling Problems:**
- Data requirements: very expensive in terms of computation and storage
- In 3D, data requirements would explode
- Currently limited to simpler games or requires much more powerful computers

**Key Takeaway:**
Transitioning away from frame-based simulation is possible but daunting - requires taking steps back in fidelity due to computational demands.

----
## Synchronizing Time in Interactive Simulations

**Key Difference: Interactive vs Non-Interactive Simulations**
- Non-interactive (e.g., Pixar movie rendering): Frame time doesn't matter, some frames can take hours
- Interactive (video games): Time must synchronize between virtual world and real-world progression
- In games, simulation time is locked to frames, but must match user expectations of real-world time progression

**Game Engine Main Loop Components**
1. Process input from user
2. Update simulation state
3. Render
4. Sleep/synchronize
5. Return to beginning

All of this must complete within target frame rate period (e.g., 1/60th of a second for 60 fps)
#### Time Constraints and Frame Period

**Target Frame Rate: 60 fps**
- Frame period: 0.01666 seconds per frame = 16.67 milliseconds = 16,666 microseconds
- Very short amount of time requires highly optimal computation
- Game development heavily focuses on efficient algorithms and optimization

**Variable Complexity in 3D Games**
- Different camera angles/directions can have drastically different rendering loads
- Looking in one direction might show more objects than another direction
- Unlike 2D games (e.g., NES Super Mario Brothers) which had constant load:
  - Fixed grid of image tiles
  - Maximum number of sprites
  - Constant rendering load frame-to-frame
- 3D games: much harder to control level of detail and computation frame-to-frame
- Frame rate can drop during complex scenes
- Need headroom in performance to handle most demanding scenes while maintaining target frame rate
#### Display Impact on Timing Schedule

**Cathode Ray Tube (CRT) Basics**
- Large, heavy vacuum container
- Electron beam shoots from gun to phosphor-coated glass
- Phosphor glows when hit by electrons
- Black and white: single beam with varying intensity (black to white)
- Color: three separate beams for red, green, blue phosphors

**Two Early Rendering Approaches:**

Display List/Vector Graphics:
- Controls beam like an Etch-a-Sketch
- List of line segments with start/end points
- Adjusts voltages to draw lines
- Used in early games like Spacewar (wireframe graphics)

Raster Display (most common):
- Pixel-based display
- Electron beam scans horizontally left-to-right, line by line
- Varies intensity per pixel according to image data
- Moves down one line after completing horizontal scan
- Phosphor glows briefly then fades, requiring constant refresh
- Display hardware controls scanning schedule regardless of computer output
- Computer must monitor display schedule to provide appropriate image data at appropriate time
#### Screen Tearing and Vertical Sync

**Screen Tearing Problem**
- Occurs when frame buffer updates while display is mid-scan
- Results in visible horizontal "tear" where old and new image data meet
- Trees or objects appear offset/sheared
- Breaks immersion and animation quality rule: images must be sufficiently similar frame-to-frame

**Vertical Sync (V-Sync) Solution**
- Synchronizes software updates with display refresh schedule
- Uses double or triple buffering:
	- Primary buffer: what display reads from
	- Secondary buffer(s): where new frames are prepared
	- Buffer flip: bulk copy or memory reference swap between buffers
- Only updates display during safe period (vertical refresh)
- Prevents screen tearing

**V-Sync Downside**
- If you miss update deadline by even 2 milliseconds, must wait entire frame period (16.67ms at 60fps)
- Causes noticeable frame rate drops
- Professional gamers often disable V-sync to minimize latency

**Modern Solution: Adaptive V-Sync**
- Takes advantage of LCD technology (works differently than CRT)
- LCDs: twist liquid crystals, no constant scanning needed
- Allows choosing your own update cycle
- If slightly miss target, slightly reduce frame rate instead of dropping entire frame
- Still avoids screen tearing
- Breaks away from legacy CRT standards
#### Input Latency

**Sources of Latency**
- Minimum latency from frame rate itself (user can't see response until displayed)
- Additional delays from input hardware (e.g., Bluetooth controllers, wireless transmission, error checking)
- Rendering based on "old" data: by the time frame finishes rendering, early information is already outdated

**When Latency Matters**
- Users notice latency beyond ~0.2 seconds (visual stimulus reaction time threshold)
- Virtual Reality: most critical - can cause simulator sickness/motion sickness
- First-person shooters and fast-paced games: significant concern
- "Game feel" games (platformers with continuous control, reaction-based): impacts control quality
- Games with indirect control: less noticeable
- Turn-based games (chess, checkers): latency not a concern, can even go below 10 fps

**Latency Reduction Techniques**

Increase frame rate:
- Higher fps = lower latency

Adaptive V-sync:
- Avoid skipping entire frames
- Display updates when software is ready

Careful with processor optimizations:
- Pipelining: good for throughput but adds latency (like assembly line - high throughput but individual items take longer)
- Cache coalescing: bundles data over time before sending, introduces latency
- These techniques improve general computing but may hurt interactivity

VR technology improvements:
- Latency can be recognized if response time is longer than visual stimulus reaction time (0.2s)
	- can cause nausea / motion sickness
- Commercial VR has driven significant latency reduction innovations

Input prediction:
- Kalman filter: form of dead reckoning
- Reduces perceived latency for continuous, predictable motion
- Used in VR head trackers

Relaxed frustum culling:
- First pass: determine what to draw based on predicted user view
- Secondary input read: final determination based on actual input
- Shrinks time between input measurement and screen display

Hardware improvements:
- Direct memory addressing
- Wider memory buses
- Higher processor clock speeds
#### What to do when?

**Why Target Consistent Frame Rate**
- Should generally aim for target frame rate (e.g., 60 fps) and sleep when done early
- No point doing more work than necessary
- Inconsistent frame rate causes inconsistent perceived latency
- Running at very fast frame rates uses more power
- Important for battery-powered devices (mobile, Nintendo Switch)
- Better power consumption when maintaining consistent target frame rate

**Time Dependency**
- Hard to guarantee frame rate will always hit target
- May occasionally slip past target even with headroom
- How you handle update portion of event loop determines whether you gracefully handle lower frame rates or handle it poorly
---
### Synchronization Real Time w Simulation - Time Dependency

**Unity Demo Overview**
Demonstration of three rotating pills (blue, red, purple) around a rolling ball to show impact of frame rate on animations. When frame rate drops from 60fps to ~20fps (due to viewing particle system and detailed terrain), the pills fall out of sync depending on their update method.
#### Three Update Modes

##### Dumb Mode (Blue Pill)
- Assumes precise target frame rate will always be hit
- Rotates constant number of degrees per frame (1 degree per frame)
- Works as intended at 60fps (60 degrees per second)
- Problem: When frame rate drops to 20fps, only rotates 20 degrees per second instead of 60
- Objects speed up and slow down as frame rate changes

Implementation:
```
new_position = old_position + constant_translation
transform.Rotate(up_vector, CONSTANT_DEGREES)
```

Advantages:
- Low overhead - no extra computation needed (only need constant offset for translation/rotation)

Disadvantages:
- Oblivious to real-world time advancement
- Inconsistent animation and gameplay experience
- Varies based on hardware and scene complexity
- Only works if you can guarantee target frame rate (easier in 2D games, hard in 3D)

##### Time-Dependent/Variable Delta Time Mode (Red Pill)
- Frame rate aware - compensates for time variations
- Uses degrees per second × Time.deltaTime
- Time.deltaTime measures elapsed time from previous frame
- Scales rotation amount to compensate when frame rate drops
- At 60fps: deltaTime = 1/60th second
- When frame rate drops: deltaTime gets bigger, more rotation applied to compensate

Implementation:
```
new_position = old_position + (velocity × delta_time)
transform.Rotate(up_vector, DEGREES_PER_SECOND × Time.deltaTime)
```

Advantages:
- Normalizes gameplay across scene complexity and hardware differences
- Compensates for frame rate variations

Disadvantages:
- Extra computation overhead (multiply operation per update)
- At extremely high frame rates, delta_t becomes tiny → floating-point rounding errors
- Worse with acceleration (involves t²) in physics-based movement

##### Fixed Update Mode (Purple Pill)
- Hybrid approach combining aspects of both methods
- Uses constant translation/rotation like dumb mode
- But runs on separate fixed update cycle managed externally
- Fixed update may be called 0 times, 1 time, or multiple times per frame depending on target
- Not running in parallel - interleaved with normal frame-based update loop

Implementation:
```
// In FixedUpdate() callback instead of Update()
transform.Rotate(up_vector, CONSTANT_DEGREES)
```
#### Fixed Update Manager

**How It Works**
Fixed update manager tracks target elapsed time and decides how many times to call FixedUpdate() to catch up:
```c#
total_delta_time = delta_time_from_previous_frame + remaining_dt_per_frame

num_fixed_updates_this_frame = floor(total_delta_time / fixed_update_period)

remaining_dt_per_frame = total_delta_time - (num_fixed_updates_this_frame × fixed_update_period)
```

**Relationship to Frame Rate**

Fixed update rate > current frame rate:
- Multiple fixed updates per frame (catch-up mechanism)
- Example: Frame rate drops to 20fps but fixed update targets 60fps → run 3 fixed updates in one frame

Fixed update rate < current frame rate:
- Some frames have zero fixed updates
- Accumulates elapsed time until enough to trigger fixed update
- Might go 1-2 frames before performing fixed update

**Important: Not Parallel Processing**
- Common misconception: fixed update runs on real-world clock in parallel
- Reality: runs in main event loop, coordinated by fixed update manager
- Single-threaded approach - fixed updates interleaved with regular updates
- Could theoretically use multithreading/multiprocessing but synchronization overhead not worth it
#### Fixed Update: Advantages and Disadvantages

**Advantages**
- Compensates for real-world elapsed time
- Game objects can be oblivious to time synchronization issues
- Objects safely assume constant elapsed time between their fixed updates
- Simplifies time-based computations (no delta_t calculations needed)
- Avoids floating-point rounding errors from small delta_t
- Physics simulations can be unstable without controlled increments - fixed update provides stability
- Can avoid expensive computation by setting fixed update rate lower than frame rate (useful for battery-powered devices)

**Disadvantages**

Runaway computational load:
- When frame rate drops → more fixed updates needed to catch up
- More fixed updates → more computation → frame rate drops further
- Can create spiral that destroys interactivity
- Need contingency plan (fixed update manager may prioritize and skip some objects)

Limited applicability:
- Can only apply to subset of objects/tasks
- Rendering must be tied to frame rate (no point rendering more often than drawing frames)
- Physics commonly runs in fixed update
- Certain game logic can use fixed update

User responsiveness issues:
- Users can only respond to state changes in normal frame-based update, not fixed updates
- Multiple fixed updates per frame = no complete feedback loop to user
- Nothing for user to see/respond to, no opportunity to poll input
- Can update user-controlled objects in fixed update, but all updates in that frame use same input

Coordination difficulties:
- Difficult to coordinate fixed update logic with normal update logic
- Objects implementing both Update() and FixedUpdate() have state changes in both
- Must carefully design logic when splitting work between update types
- Rendering-related: probably only in Update()
- State changes needing fixed schedule: in FixedUpdate()

Jitter problems:
- Updating on different rate than visual display causes jerky motion
- Example: fixed update rate lower than frame rate
- Some frames skip fixed update → object doesn't move → herky-jerky appearance
- Solution: interpolate or extrapolate between fixed updates in normal update cycle

**Interpolation/Extrapolation Solution**
- Example: fixed update at 30fps, frame rate at 60fps
- Extrapolate from previous two fixed updates
- Assume same course of motion continues
- Update transform/position for graphics display
- Result: constant perceived movement on screen even without running fixed update every frame
#### Time Dependencies in Games

**Everything Has Time Dependencies**
Safe assumption: assume everything in your game depends on time

Moving game objects:
- Have velocity
- Need elapsed time to calculate displacement

Acceleration:
- Same time-dependency as velocity

Animation systems:
- Character limbs, animation frames
- Must pick appropriate frame or interpolate based on elapsed time

Physics simulation:
- Important for stability
- Typically relies on fixed update schedule

Artificial intelligence:
- Decisions made more often at high frame rates
- Should use fixed schedule or time-dependent schedule

Probabilistic behavior:
- Rolling dice more often at high frame rate → events happen more frequently
- Must normalize likelihood according to time to maintain consistent behavior
---
### Modern Game Engine

**Definition**
Modern game engine includes both:
- Runtime generation/rendering of the game (computational kernel)
- Tools used to create the game (software framework for development and deployment)

Examples: Unity, Unreal - can run games live AND provide development tools
#### Early Influences on Game Engines
**Sketchpad (Ivan Sutherland)**
- Research project oriented towards design and spatial relationships
- Used for architectural drawings, floor plans, layouts
- Modeled geometric representations, spatial relationships, constraints
- Built around concept of digital drafting table
- Influential on 3D modeling software, game level editors, tools working with 3D data
- Key features demonstrated:
	- Drawing lines and shapes with constraints (horizontal, vertical, etc.)
	- Moving points and connected lines together
	- Understanding geometry relationships
	- Constraint satisfaction system
	- Zoom and rotation capabilities
	- Large virtual workspace ("paper" approximately 2 miles × 2 miles)

**HyperCard (Apple Macintosh)**
- Commercial software with WYSIWYG (What You See Is What You Get) editing
- One of first software packages with live preview
- Created interactive multimedia experiences
- Could make video games: draw 2D artwork, attach event callbacks (clicks), generate animations and audio
- No compiler/linker needed - immediate testing (like Unity's play button)
- High-level integrated code editor with special scripting language
- Example game: "The Manhole" - first-person point-and-click adventure
- Enabled much tighter development turnaround than traditional build processes

**Impact**
These early works heavily influenced:
- Game engine tools (Unity, Unreal)
- Art software (Photoshop-type applications)
- 3D modeling software
- Level editors
#### Early Internal Game Engine Tools
**Text Adventures**
- Early PCs only capable of rendering text (for business purposes: documents, spreadsheets)
- No graphical capabilities but developers still created games
- Players read prose and responded by typing commands ("move rug", "grab lamp")
- Example: Z-Machine by Infocom
	- Virtual machine concept for portability
	- Write interpreter once, run games on multiple platforms (IBM PC, Amiga, Atari, Commodore, etc.)
	- Separated technical expertise (computer scientists) from creative work (writers)
	- Tools made for domain experts to work with human-readable forms

**Point-and-Click Adventures**
- When graphics became available (Sierra, LucasArts)
- Example: SCUMM engine (originally for Monkey Island, reused for many games)
- Tools made for artists to work with images and animation sequences
- Internal tools required training but made companies more effective

**Third-Party Game Engines**
- Meant for average users/computer users
- Examples: RPG Maker, Adventure Game Maker (multiple versions, late 80s/early 90s)
- Early versions were limited, forced developers to work within particular game genre
- Limitations due to computer capabilities and figuring out how to make effective, easy-to-use tools
#### First-Person Shooter Influence
**Id Software Games**
- Wolfenstein, Doom, Quake were highly influential
- Large fan base reverse-engineered and modified games
- Hobbyists created shareware editors (Doom Editor, Quake Editor)
- Id embraced community modifications
- Could not give away internal tools (licensing engines for $1 million+, required specialized workstations)

**Level Editors**
- Similar to CAD software and 3D editing software
- Features reminiscent of Sketchpad
- Laid out walls, transitions between rooms/areas
- Built levels for deployment in actual games

**Impact and Licensing**
- Small number of people had technical skills to develop these engines (required highly optimized software)
- Hardware barely capable in early days (required experts like John Carmack)
- Engines reused extensively through licensing
- Family tree of game engines shows influences and licensing relationships between developers
#### Features of Modern Game Engines
**Declarative Creation**
- Often configure rather than write procedural code
- Define constraints declaratively
- Constraints interpreted to produce desired game object behavior
- Can extend capabilities by writing procedural code for custom features
- Extensions become part of declarative toolkit

**Platform Abstraction**
- Deploy to multiple platforms easily (PlayStation 5, PC, Mac, etc.)
- Same data models and artwork across platforms
- Behind-the-scenes configuration tailored for appropriate hardware

**Integrated Development Environment (IDE)**
- Similar to 3D software, CAD, Sketchpad
- Similar to HyperCard features
- WYSIWYG live editing
- Asset management and content pipeline
- Workflow support for organizing media content

**Stand-Alone Capability**
- Can build entire game using just the engine
- May need supplementary tools: 3D modeling (Blender), Photoshop, audio editor
- Most work can be done in game engine

**Genre Flexibility**
- Not forced to make one type of game
- Can make any game imaginable with modern feature-complete engines
#### Components of Modern Game Engine
**Computational Kernel**
- Live simulation part of game engine
- Frame-based event loop

**Input Management**
- Very efficient code for low latency
- Platform abstraction
- Support variety of controllers across different systems

**Graphics Rendering Engine**
- Support for canonical rendering pipeline with GPU acceleration
- Geometry/graphics tools:
	- Scene graph: manages spatial relationships
	- Space/volume partitioning: manages detail and determines important parts of game world
	- Linear math: routines and API support for common graphics computations

**Physics Engine**
- Essentially a constraint solver
- Simultaneous simulated world coexisting with graphics and audio
- Game engine maintains synchronization between subsystems

**Artificial Intelligence (AI)**
- Standard libraries for path planning
- Behavior implementation and planning
- Agent management
- Time scale support

**Networking**
- Platform abstraction challenges (big/little endian, etc.)
- Event synchronization: map user input to network messages, game object state changes
- Information prioritization: can't send everything
- Predictions: guess what remote players are doing for responsive simulation

**Event-Based Architecture**
- Loosely coupled code tied to events
- Game objects consume events without knowing about event emitters
- Reduces dependencies between game objects

**Scheduling**
- Manages when updates occur (frame-based updates vs. fixed updates)

---
## Game Engine Summary
- A closed-loop sensory simulation meant to convince a game player that a virtual world exists and can be interacted with in real time
- A simulation base done on a rapid sequence of frames
	- like frozen slices of time
	- present them to the user so that user can respond to what they see
	- done so quickly enough to trick the user into thinking there's a constant simulation rather than discrete frames
- A constraint solver declaratively defined by the game designer and further extendable via event-based callbacks/handlers
	- events are generated by the constraint solver, user input, or connected system
- A set of interactive tools supporting creation, development, and deployment of a game