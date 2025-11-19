# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a standalone, single-file HTML application for visualizing and analyzing convenience store locations in Nagoya City. It uses Leaflet.js with GeoJSON data and provides interactive mapping, filtering, heatmap visualization, cluster analysis, and data export capabilities.

**Key Point**: This is a purely client-side application with no build process, backend, or dependencies to install. All libraries are loaded via CDN.

## Architecture

### Single-File Structure
The entire application is contained in `index.html`:
- **HTML**: Minimal structure with control panel, map container, and stats dashboard
- **CSS**: Embedded styles for responsive layout and UI components
- **JavaScript**: All application logic is inline, including:
  - Data generation (`generateStoreData()` - creates synthetic demo data)
  - Map rendering with Leaflet.js
  - Filter logic (brand and ward-based)
  - Visualization modes (markers, heatmap, clusters)
  - GeoJSON export functionality

### Data Model
The application uses GeoJSON `FeatureCollection` format:
```javascript
{
  type: 'FeatureCollection',
  features: [{
    type: 'Feature',
    geometry: { type: 'Point', coordinates: [lng, lat] },
    properties: { id, name, brand, ward, 営業時間, 駐車場 }
  }]
}
```

**Important**: Currently uses synthetic data generated in `generateStoreData()`. To use real data, replace this function with a `fetch()` call to load an external GeoJSON file (see README.md for data sources).

### Key Global Variables
- `map`: Leaflet map instance
- `currentLayer`: Currently displayed layer (markers/heatmap/clusters)
- `storeData`: Complete GeoJSON FeatureCollection
- `NAGOYA_WARDS`: Array of 16 ward definitions with center coordinates
- `BRANDS`: Object mapping brand names to hex colors

### Layer Management Pattern
The app switches between three visualization modes by:
1. Removing `currentLayer` from map
2. Creating new layer (marker/heatmap/cluster)
3. Adding new layer and updating `currentLayer` reference

## Development Workflow

### Testing Changes
```bash
# Option 1: Open directly in browser
open index.html  # macOS
xdg-open index.html  # Linux
start index.html  # Windows

# Option 2: Use local server (recommended for avoiding CORS issues)
python -m http.server 8000
# Then visit http://localhost:8000

# Option 3: VS Code Live Server
# Right-click index.html → "Open with Live Server"
```

### No Build/Compile Step
This project has no build process. Edit `index.html` and refresh the browser.

### Git Workflow
```bash
# Standard workflow
git add index.html README.md
git commit -m "Description"
git push

# GitHub Pages automatically deploys from main branch
# Changes appear at https://kkawailab.github.io/nagoya_convenience_stores/
```

### Repository Management
```bash
# Update repository metadata
gh repo edit --description "..." --homepage "..."
gh repo edit --add-topic topic-name

# View repository info
gh repo view
```

## Code Modification Guidelines

### Adding New Brands
1. Add to `BRANDS` object with hex color
2. Add option to `#brandFilter` select element
3. Update `applyFilters()` logic if needed

### Adding New Wards
1. Add to `NAGOYA_WARDS` array with center coordinates
2. Option automatically added to `#areaFilter` in initialization code

### Modifying Visualization Parameters
- **Heatmap**: Adjust `radius`, `blur`, `gradient` in `showHeatmap()`
- **Clusters**: Modify `maxClusterRadius`, `spiderfyOnMaxZoom` in `showClusters()`
- **Marker styles**: Edit `pointToLayer` function in `createMarkerLayer()`

### Exporting Filtered Data
The `exportGeoJSON()` function:
- Respects current filter state (brand + ward)
- Generates filename with filters and timestamp
- Uses Blob API with `application/geo+json` MIME type
- Shows confirmation with store count

## External Dependencies (CDN)

All loaded from unpkg.com:
- **Leaflet.js 1.9.4**: Core mapping library
- **Leaflet.heat 0.2.0**: Heatmap visualization
- **Leaflet.markercluster 1.5.3**: Marker clustering

CSS and JS are loaded in `<head>` and before closing `</body>` respectively.

## Deployment

The application is hosted on GitHub Pages from the `main` branch. Any push to `main` automatically deploys to:
https://kkawailab.github.io/nagoya_convenience_stores/

To enable GitHub Pages:
1. Repository Settings → Pages
2. Source: Deploy from branch `main`
3. Save

## Data Sources (Future Enhancement)

Current implementation uses synthetic data. For real data integration, see README.md section "データについて" which documents:
- Overpass API (OpenStreetMap)
- 国土地理院 地理院地図
- 名古屋市オープンデータ
- Google Places API

Replace `generateStoreData()` call with `fetch('data/stores.geojson')` to load external data.
