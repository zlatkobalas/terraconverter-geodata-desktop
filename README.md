# TerraConverter geodata (desktop)

Pre-built geospatial datasets consumed by [TerraConverter](https://github.com/zlatkobalas/TerraConverter)'s desktop app (Settings → External Data → Download/Update). This repository holds **release assets only** — no application code, no issue tracking.

It is separate from [`terraconverter-geodata`](https://github.com/zlatkobalas/terraconverter-geodata) (the Android address index repo): that repo's downloader calls GitHub's `/releases/latest` endpoint, which returns only the single most recent release for the *whole* repo. Publishing independently-versioned desktop datasets there would intermittently hide the Android asset behind a unrelated "latest" release. Keeping them apart avoids that regression entirely.
  
## Datasets

Each dataset is versioned and released **independently** — they update on different schedules (WMM roughly every 5 years, DGU addresses more often, geoid/admin boundaries rarely), so bundling them under one tag would force unrelated re-downloads. The downloader (`terraconverter.gui.geodata_download`) lists all releases and, per dataset, uses the newest one whose assets contain that dataset's file — there's no single "overall" repo version.

| Dataset | Tag prefix | Asset | Contents |
|---|---|---|---|
| Geoid model | `geoid-v*` | `egm2008-5.pgm.gz` | EGM2008 geoid grid, 5' resolution |
| World Magnetic Model | `wmm-v*` | `WMM.COF` | NOAA/NGA WMM coefficients |
| Administrative boundaries | `admin-boundaries-v*` | `admin_boundaries.zip` | `country/`, `county/`, `lgu/`, `settlement/` shapefiles (DGU INSPIRE Administrative Units, simplified) |
| DGU addresses | `addresses-v*` | `Address.bundle.zip` | `Address.gml.gz` (source) + `Address.sqlite` (pre-built FTS5 index) |
| DGU place names | `toponyms-v*` | `GeographicalNames.bundle.zip` | `GeographicalNames.gml.gz` (source) + `GeographicalNames.sqlite` (pre-built FTS5 index) |
  
## Licensing / attribution

- **DGU addresses, DGU place names, administrative boundaries** — source: [Državna geodetska uprava (DGU)](https://dgu.gov.hr), under Croatia's open data licence ("Otvorena dozvola", <https://data.gov.hr/otvorena-dozvola>): free for any use, commercial or non-commercial, with attribution and the date of last modification.
- **Geoid model (EGM2008)** — source: NGA (US National Geospatial-Intelligence Agency), public domain.
- **WMM coefficients** — source: NOAA/NGA World Magnetic Model, public domain.

Pre-built SQLite indexes (addresses/place names) are derived works built by [`terraconverter`](https://github.com/zlatkobalas/TerraConverter)'s own indexing code from the DGU GML source included alongside them in the same archive.
  
## Publishing a new version
  
1. Build the dataset locally (see the corresponding `README.md` under`external_data/<dataset>/` in the main repo).
2. Create a new release here tagged `<dataset>-v<N>` (increment only the `N` for that one dataset — other datasets' tags are untouched).
3. Attach exactly the one asset listed above for that dataset. GitHub computes a SHA-256 `digest` on the asset automatically; the desktop app verifies against it before installing — no separate checksum file needed.
4. Nothing else to configure — the app picks up the new release the next time a user clicks "Update" on that row in Settings → External Data. 
