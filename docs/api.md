# API Specification

The Interactive Mechanics Playground backend exposes a clean REST API built with FastAPI and Pydantic v2.

Interactive OpenAPI documentation is available in a running instance at:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

---

## 1. Endpoints Overview

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/health` | Healthcheck and service readiness |
| `GET` | `/api/v1/examples` | Returns pre-configured analytical benchmark examples |
| `POST` | `/api/v1/analyze` | Solves beam model and returns reactions, diagrams, and extrema |

---

## 2. Request & Response Schemas

### `POST /api/v1/analyze`

#### Request Body (`BeamAnalysisRequest`)
```json
{
  "length": 6.0,
  "youngs_modulus": 200000000000.0,
  "cross_section": {
    "section_type": "rectangular",
    "width": 0.25,
    "height": 0.4
  },
  "supports": [
    { "type": "pin", "position": 0.0 },
    { "type": "roller", "position": 6.0 }
  ],
  "point_loads": [
    { "position": 3.0, "force": -24000.0 }
  ],
  "distributed_loads": [],
  "applied_moments": []
}
```

#### Response Body (`BeamAnalysisResponse`)
```json
{
  "success": true,
  "reactions": [
    { "position": 0.0, "type": "pin", "force_y": 12000.0, "moment_z": 0.0 },
    { "position": 6.0, "type": "roller", "force_y": 12000.0, "moment_z": 0.0 }
  ],
  "equilibrium": {
    "sum_fy": 0.0,
    "sum_moment_z": 0.0,
    "is_equilibrated": true
  },
  "curves": {
    "x": [0.0, 0.12, "...", 6.0],
    "shear": [12000.0, 12000.0, "...", -12000.0],
    "moment": [0.0, 1440.0, "...", 0.0],
    "deflection": [0.0, -0.00003, "...", 0.0],
    "rotation": [-0.00036, -0.00035, "...", 0.00036],
    "bending_stress_top": [0.0, -432000.0, "...", 0.0],
    "bending_stress_bottom": [0.0, 432000.0, "...", 0.0]
  },
  "extrema": {
    "max_shear": { "value": 12000.0, "x": 0.0 },
    "min_shear": { "value": -12000.0, "x": 6.0 },
    "max_moment": { "value": 36000.0, "x": 3.0 },
    "min_moment": { "value": 0.0, "x": 0.0 },
    "max_deflection": { "value": 0.0, "x": 0.0 },
    "min_deflection": { "value": -0.00081, "x": 3.0 },
    "max_stress_magnitude": { "value": 10800000.0, "x": 3.0 }
  },
  "section_properties": {
    "area_moment_of_inertia": 0.0013333333333333333,
    "outer_fiber_distance": 0.2
  },
  "warnings": []
}
```

---

## 3. Error Responses

- `422 Unprocessable Entity`: Validation failure (e.g., negative length, supports outside beam bounds, invalid section type).
- `400 Bad Request`: Mechanically unstable configuration (e.g., rigid-body translation or rotation unconstrained).
