# Aquatic Vegetation Detection in the Aegean Sea using Sentinel-2

Detection of floating aquatic vegetation and algae in the waters between Samos Island (Greece) and the Turkish coast using multitemporal Sentinel-2 satellite imagery and Google Earth Engine.

This project was developed as part of a volunteer initiative focused on environmental monitoring of coastal Mediterranean ecosystems.
The aim of this project is to detect and map aquatic vegetation - including floating algae, phytoplankton blooms, and potential Posidonia oceanica meadows — on the sea surface in the study area, using spectral indices derived from multispectral satellite data.
### Version 1 — NDVI + NDWI approach (initial)

The first version combined two spectral indices:
- **NDVI** *(Normalized Difference Vegetation Index)* — NIR (B8) and Red (B4)
- **NDWI** *(Normalized Difference Water Index)* — Green (B3) and NIR (B8)

# Pixels classified as: no vegetation (NDVI ≤ 0.05) OR water (NDWI ≥ 0)
vegetation_mask = ndvi_image.lte(0.05)
water_mask = ndwi_image.gte(0)
combined_mask = vegetation_mask.Or(water_mask)

**Limitation:** This approach *excluded* rather than detected aquatic vegetation. NDVI on water pixels was found to be ≈ -0.006, indicating no vegetation signal was captured.

---

### Version 2 - FAI-based approach (improved)
The improved version uses the **Floating Algae Index (FAI)**, specifically designed to detect surface algae and floating vegetation in marine environments.

**FAI formula:**
FAI = NIR − [Red + (SWIR − Red) × (λNIR − λRed) / (λSWIR − λRed)]
Where: NIR = B8, Red = B4, SWIR = B11

# Detect aquatic vegetation: FAI > 0.02 AND water (NDWI ≥ 0)
water_mask = ndwi_image.gte(0)
aquatic_veg_mask = fai_image.gt(0.02).And(water_mask)
smoothed_mask = aquatic_veg_mask.focal_median(radius=4)
```

**Results:**
| Index | Mean value | Interpretation |
|---|---|---|
| FAI (full AOI) | **0.043** | Above threshold → algae/vegetation detected ✅ |
| NDAVI (on water) | -0.10 | No submerged vegetation signal |
| NDVI (on water) | -0.006 | No surface vegetation with V1 method |

The detected clusters likely represent **floating algae or phytoplankton blooms** at the sea surface.


## License
This project was created for non-commercial, volunteer environmental monitoring purposes.
