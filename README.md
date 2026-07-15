# GPX Toolbox

A single-file browser app for viewing and editing GPX activity files. No install, no server, no account — open `index.html` directly in any modern browser.

## Features

### View
- Renders your track on an OpenStreetMap base layer
- Elevation profile chart in the sidebar
- Full point list with coordinates and timestamps

### Crop
- Trim the start and end of an activity using range sliders
- Cropped-out points are dimmed on the map and excluded from export

### Delete points
- Switch to **Delete mode** and click any point on the map or in the list to remove it
- Undo removes points one at a time
- Removed points are excluded from export

### Fill route gaps
Detects sections where recording was interrupted (app crash, GPS loss) by looking for large jumps between consecutive points. Detected gaps are shown as orange dashed lines on the map.

To fill a gap:
1. Click **Fill** next to the gap in the sidebar
2. Optionally click the map to place waypoints that guide the route
3. Choose a movement profile — **Cycling** or **Hiking / Walking**
4. Click **Route it** — the app queries [OSRM](https://project-osrm.org/) to find a real road or trail route between the endpoints
5. If the file has timestamps, a **Pause at start** field appears, pre-filled with the difference between the total time gap and the estimated travel time. Adjust it if needed (e.g. the gap includes a lunch stop)
6. Review the purple preview route on the map, then click **Accept**

Filled points get realistic timestamps using terrain-aware pacing:
- Real elevation is fetched from [OpenTopoData](https://www.opentopodata.org/) (SRTM 90m) along the routed path
- Speed is estimated per segment using **Tobler's hiking function** (hiking profile) or a gradient-adjusted cycling model
- The pause is placed at the start of the gap; movement timestamps are then scaled using terrain pacing to land exactly on the next recorded point's time, keeping the full activity continuous

Filled points are marked as synthetic and carry only coordinates, elevation, and time — no invented heart rate, cadence, or other sensor data.

### Stitch two activities
Click **Stitch activity…** in the header to merge a second GPX file into the current one. If both files have timestamps the order is determined automatically; otherwise the second file is appended. After stitching, the junction between the two activities is treated as a regular gap and can be filled using the gap-fill workflow above.

A typical workflow for two activities recorded on the same outing:
1. Load the first file
2. Crop it if needed
3. Click **Stitch activity…** and load the second file
4. Fill the junction gap if desired
5. Export the merged result

### Edit timestamps
If your file has timestamps, a **Timestamps** section lets you change the date on all points at once (e.g. if the device had the wrong date set). Time-of-day and the relative spacing between points are preserved. This is useful before stitching if the two files were recorded on different calendar dates.

### Export
Exports a clean GPX file containing only the active (non-removed, in-crop) points. Original metadata is preserved: file `<metadata>`, track name and type, and all per-point data including `<extensions>` carrying heart rate, cadence, power, and temperature.

## Responsible use

This tool is intended to restore continuity to activities where recording genuinely failed — a crashed app, a drained battery mid-ride, or a GPS dropout on a tunnel section.

**The gap-fill feature should not be used to fabricate activity data**, for example to inflate distance or moving time on platforms like Strava, Garmin Connect, or in any competitive context. Filled segments are approximations based on map routing and terrain models, not actual recorded movement.

Use it honestly.

## Privacy

Everything runs locally in your browser. GPX files are never uploaded anywhere. The only outbound requests are:
- **OSRM** (`router.project-osrm.org`) — to route the gap path
- **OpenTopoData** (`api.opentopodata.org`) — to fetch terrain elevation along the route

Both are free public APIs. Neither receives your full track, only the coordinates involved in a specific gap fill.

## Dependencies

- [Leaflet](https://leafletjs.com/) 1.9.4 — map rendering
- [OpenStreetMap](https://www.openstreetmap.org/) — map tiles
- [OSRM](https://project-osrm.org/) — routing
- [OpenTopoData](https://www.opentopodata.org/) — elevation (SRTM 90m)
