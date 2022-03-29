# Games 104 Chapter 1

Modern Game Engine - From Getting Started To Practice

https://www.bilibili.com/video/BV1oU4y1R7Km

Lecturer: Xi Wang

## History

### Early Age

From early age of Games, there's no such concepts as Game Engine. Developers were mainly dealing with limited memory and performance. A Golden Age of Game Dev.

Father of Game Engine -> John Carmack 

- Wolfenstein 3D (1992)
- Doom
- Licensed his engine to Raven -> ShadowCaster

Concept: Separating execution of core functionality by the game engine from the creative assets.

- Quake -> OpenGL on **Graphics Card** -> full real-time 3D rendering / Network sync

!! Driving Force for **Game Engine Dev** -> Hardware Developement

### Current Age

Zoo of Game Engine:

- Commercial Engine: Unreal, Unity, CryEngine
- In-house Engine: Frostbite, Source, Rage, Infinity Ward
- Free Engine: Stride, Godot, O3DE (Light Weight)

Middlewares:

- Physics & Animation: Havoc, PhysX
- Sound: Wise
- Rendering: True Sky, Enlighten
- Other: Speedtree (lord of ring), Simplygon

## What's Game Engine

- Techonology Foundation of Matrix
  - Define the differences between real and unreal, true world and virtual world
- Productivity Tools of Creation
- Art of Complexity
  - imperfect, compromises



Complex, but basic simulation by 0/1 (Turing machine)

Computer Graphisc takes only 10% of the Game Engine

### Real-time

God with Limited Power in **Realtime**

- Frame Rate
- Hardware power

### Toolchain

Massive Toolchain for creators from **difference background and ability** (Artist, Designer, Other Programmers)

Developer Platform

- for Programmer -> Expandable API
- for Studio -> **Collaborate** hundreds of developers smoothly

### On Fly

update the engine on the fly ~ just like replace the whole plane on the flight

Understand the **Flow**

## How to Study

Focus on the **Framework**!!

Source Syllabus

- Basic Elements (Structure and Layer, Data Organization and Management :: MVVM)
- Rendering (Model, Material, Shader, Texture, Light, Shadow, Render Pipeline, Sky, Terrain)
  - Culling -> Rendering -> Post-processing
  - Deffered Rendering, Forward Rendering
- Animation (Basic Concepts, Animation Structure, Pipeline)
- Physics (Gameplay Applications, Performance Optimization)
- Gameplay （Event System, Script System, Graph Driven)
- Misc System (VFX, Navigation, Camera)
- Tool Set 
  - C++ Reflection -> expose variables and function to be used in editor
  - Data Schema (Upgradable, Extendable)
- Online Gaming (Lockstep Sync, State Sync, Consistency)
- Advanced Tech
  - Motion Matching (matching and searching)
  - Procedural Content Generation (PCG)
  - Data-Oriented Programming (DOP) (Multi-core structure)
  - Job System (Multi-core structure)
  - Lumen (Unreal5's new **fully dynamic global illumination & reflection system**)
  - Nanite (UE5's new virtualized **geometry system**)

