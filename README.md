# Karlskrona Impact Risk Engine

Built at the **EDTH hackathon in Karlskrona, Sweden (May 2026)** as a team project.

When a hostile drone or missile is intercepted over a populated area, the interception can be successful and still cause harm: the wreckage and fuel have to land somewhere. This engine answers the question *where would it be unacceptable for debris to fall?* It scores the Karlskrona archipelago cell by cell for how unsuitable it is as a debris-fall area, so an intercept can be planned for a time and place where fallout does the least damage.

## What it does
- **Scores a hexagonal grid.** The region is divided into H3 resolution-9 cells. Each cell gets an unsuitability score and a Target Equivalent Level (TEL) that combines:
  - **Population:** Swedish SCB 1 km population grid.
  - **Land use:** OpenStreetMap geometry (residential, industrial, commercial, roads, water, forest).
  - **Maritime traffic:** shipping layers and mock AIS vessel positions, which raise risk around ferries, passenger ships, cargo and tankers.
  - **Time and weather:** presence curves for rush hour, night and weekends, adjusted by a live weather feed (Open-Meteo).
  - **No-fall zones:** critical infrastructure such as naval bases, airfields, substations and power plants, which are given a very high penalty so debris is steered away from them.
- **Simulates interceptors.** Engagement paths for RBS 70 Mk2, IRIS-T SLM, Stinger, Kreuger 100 / 100XR and JAS 39 Gripen (scrambled from F17 Ronneby), each with its own speed, range and reaction time.
- **Models debris fallout.** A Gaussian footprint that depends on debris class (heavy, medium, fine/fuel), altitude, terminal velocity, drag and wind drift.
- **Predicts the threat's route.** Intent inference over candidate targets, a Kalman-style drone tracker, A* route prediction over the risk grid and multi-hypothesis route sampling.
- **Shows it on a map.** A lightweight Python HTTP server with a Leaflet.js interface, where time, weather and interceptor parameters can be changed by hand.

## Project structure
| Path | Purpose |
|------|---------|
| `app.py` | Entry point: builds or loads the scored grid and starts the web server (port 8000) |
| `api/` | HTTP server and the Leaflet front end |
| `core/` | Configuration, interceptor and infrastructure definitions, shared state |
| `data_processing/` | Loaders for OSM, SCB population, AIS, maritime layers and weather |
| `engine/` | Cell scoring, debris-fallout physics, drone behaviour profiles |
| `prediction/` | Intent inference, tracking filter, A* routing, probabilistic route sampling |
| `data/` | Population grid and raster, mock AIS data, presence curves |
| `tests/` | Tests for routing, prediction and integration |
| `scripts/` | One-off refactoring helpers used during the hackathon |

## Run it
```bash
pip install -r requirements.txt
python app.py
```
Then open http://localhost:8000. The first start builds the scored grid if `risk_cache_v4.json` is missing or out of date. Weather is fetched live, so an internet connection is needed. The map uses CartoDB dark tiles.

## Team
Built by the hackathon team, in alphabetical order by GitHub handle:
- [@GuddetiAnisha](https://github.com/GuddetiAnisha)
- [@itssaideep](https://github.com/itssaideep): modular refactor of the codebase
- [@sriyaaa2003](https://github.com/sriyaaa2003)
- [@sumedhpisapati](https://github.com/sumedhpisapati)

The original team repository is [sumedhpisapati/Wnts](https://github.com/sumedhpisapati/Wnts). This repository is a copy of the project, imported without the original commit history.

## Notes
This is a hackathon prototype built on public and mock data, not an operational targeting or defence system.
