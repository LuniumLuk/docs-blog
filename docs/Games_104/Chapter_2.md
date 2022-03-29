# Games 104 Chapter 2

Modern Game Engine - From Getting Started To Practice

https://www.bilibili.com/video/BV1oU4y1R7Km

Lecturer: Xi Wang

## Layered Architecture of Game Engine

- Tool Layer
- Function Layer
  - Rendering, Animation, Gameplay ...
- Resource Layer
- Core Layer
  - Thread Pool, Memory Allocation, Math, Container
- Platform Layer

### Why Layaered

- Decoupling and Reducing Complexity
  - Independent
- Response for Evolving Demands
  - Upper -> evolve fast
  - Lower -> Stable

with Middleware and 3rd Party Libraries

## Animated Character as Example

### Resource Layer

**Assets**
- Unify file format, Engine friendly file format
- A composite file to refer to all resource
- GUID, a unique identifier

**Manage**
- Handle system
- Life Cycle
  - Garbage Collection, Deferred Loading

### Function Layer

> boundry is blurred between Engine and Specific Game

- One Tick a cycle
- TickLogic
  - Camera
  - Motor
  - Controller
  - Animation
  - Physics
  - ...
- TickRender
  - RenderCamera
  - culling
  - rendering
  - postprocessing
  - present

- Multi-Threading
  - Fixed Thread
  - Mainstream -> Thread Fork/Join
  - JOB System -> Atomic Jobs distributtion (More Adcanced, but hard to achieve)

### Core Layer

- Math Lib (mainly linear algebra)
  - Math Efficiency (Carmack's)
  - SIMD
- Data Structure and Containers
  - Array
  - Link List
  - Queue
  - Heap
  - Tree
  - Graph
  - Stack
  - Hashing
- Memory Management
  - put all data together
  - data are in order
  - access data together

### Platform Layer

- Render Hardware Interface (RHI)
  - layer connect to -> OpenGL, DirectX, Vulcan ...
- Hardware Architecture

### Tool Layer
- Editors
- DCC - Digital Content Creation
  - Asset Conditioning Pipeline (FBX, USD)
  - Other Software -> Our Engine

