# Terra-Weather

A procedural 3D terrain, climate, and weather simulation built in C++ using computer graphics and ray tracing techniques.

Terra-Weather procedurally generates a terrain environment and combines it with atmospheric, climate, cloud, and weather systems. The project explores procedural generation, real-time graphics, ray tracing, shader programming, and performance optimization.

## Features

* Procedurally generated terrain using layered noise
* Dynamic climate and weather simulation
* Volumetric cloud rendering
* Day/night lighting controls
* Interactive 3D camera movement
* CPU-based ray tracing
* GPU rendering support
* Progressive rendering that improves image quality while the camera remains still
* Multithreaded tile-based rendering
* Terrain materials for grass, rock, and snow
* Screenshot capture
* Random terrain regeneration using different seeds

## Tech Stack

**Language**

* C++17

**Graphics**

* OpenGL 3.3+
* GLFW
* GLAD
* GLM
* GLSL shaders

**Build System**

* CMake 3.20+

**Rendering**

* CPU ray tracer
* GPU rendering infrastructure
* Progressive sampling
* Multithreaded tile rendering
* Bounding Volume Hierarchy (BVH) acceleration
* Shadow rays and direct sunlight sampling

## Procedural Terrain Generation

Terrain is generated using procedural noise rather than predefined heightmaps.

The terrain system uses:

* 2D Perlin noise
* Fractal noise generated from multiple Perlin-noise octaves
* Adjustable persistence
* Adjustable lacunarity
* Seed-based procedural generation

Multiple octaves of noise are combined at different frequencies and amplitudes to produce more natural terrain features.

The default terrain generation configuration uses six octaves with configurable scale, persistence, and lacunarity.

The project also uses **3D Perlin noise** to perturb volumetric cloud density and create more organic cloud formations.

## Shaders

The project contains GLSL shaders for GPU rendering:

```text
shaders/
├── display.vert
├── display.frag
└── raytrace.comp
```

* `display.vert` — vertex shader used for displaying the rendered image
* `display.frag` — fragment shader used during image presentation
* `raytrace.comp` — compute shader used by the GPU ray-tracing pipeline

## Architecture

The project is separated into several major systems:

```text
Terra-Weather/
├── include/
│   ├── Camera.h
│   ├── Climate.h
│   ├── CloudMap.h
│   ├── HeightMap.h
│   ├── NoiseGen.h
│   ├── Atmosphere.h
│   ├── BVH.h
│   ├── Materials.h
│   ├── CPURenderer.h
│   ├── GPURenderer.h
│   └── WorldData.h
│
├── src/
│   ├── core/
│   ├── terrain/
│   ├── raytracer/
│   ├── climate/
│   └── main.cpp
│
├── shaders/
│   ├── display.vert
│   ├── display.frag
│   └── raytrace.comp
│
└── CMakeLists.txt
```

### Core

Handles the application window, camera controls, framebuffer, and other low-level rendering infrastructure.

### Terrain

Generates height data using procedural Perlin and fractal noise.

### Ray Tracer

Handles scene intersection, materials, terrain geometry, lighting, atmospheric effects, clouds, and ray-tracing calculations.

### Climate

Generates environmental data including humidity, wind, precipitation, weather, and cloud information.

### Shared World Data

The simulation systems communicate through shared world data so that terrain, climate, clouds, and rendering all use the same generated environment.

## Rendering Strategy

To maintain responsiveness while rendering computationally expensive scenes, the renderer uses progressive rendering.

When the camera moves or a new terrain seed is generated:

1. The framebuffer resets.
2. Rendering begins at reduced resolution with one sample per pixel.
3. A multithreaded tile-based renderer distributes work across available CPU threads.
4. After several preview samples, rendering switches to full resolution.
5. Additional samples continue accumulating until the camera moves again.

Ray paths use Russian roulette termination after several bounces to reduce unnecessary computation.

## Controls

| Input              | Action                                      |
| ------------------ | ------------------------------------------- |
| `W A S D`          | Move horizontally                           |
| `Shift`            | Sprint                                      |
| `Q / E`            | Move down / up                              |
| Right Mouse + Drag | Look around                                 |
| `R`                | Generate a new world seed                   |
| `[` / `]`          | Move backward / forward through time of day |
| `F12`              | Save a screenshot                           |
| `Esc`              | Exit                                        |

## Building the Project

### Requirements

* C++17-compatible compiler
* CMake 3.20+
* OpenGL 3.3+ compatible graphics drivers

GLFW, GLM, and GLAD are downloaded automatically through CMake.

### Build

```bash
git clone https://github.com/ummjanavi/Terra-Weather.git
cd Terra-Weather

cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
```

Run the program:

```bash
./build/ProceduralWorld
```

Linux users may also need the development packages required by GLFW for X11 or Wayland.

## Optional Textures

Terrain textures can be placed in:

```text
assets/textures/
```

Supported texture names include:

```text
grass.png
rock.png
snow.png
```

If textures are not present, the renderer automatically falls back to solid terrain colors.
