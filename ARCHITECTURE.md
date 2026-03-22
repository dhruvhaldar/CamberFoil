# CamberFoil Architecture

CamberFoil uses a modern monorepo architecture designed for seamless deployment on Vercel. This leverages Vercel's ability to host static frontend assets while automatically provisioning serverless functions for the Python backend.

## Directory Structure

```text
CamberFoil/
├── api/                  # Python Serverless Backend
│   └── index.py          # FastAPI application entry point
├── public/               # Static assets (images, fonts, etc.)
├── src/                  # React + Vite Frontend
│   ├── assets/
│   ├── components/       # UI Components (Glassmorphism cards, Canvas)
│   ├── App.jsx
│   └── main.jsx
├── index.html            # Vite HTML entry point
├── package.json          # Node dependencies and build scripts
├── pyproject.toml        # Python project metadata (managed by uv)
├── requirements.txt      # Exported Python dependencies for Vercel build
├── vercel.json           # Vercel deployment routing configuration
└── vite.config.js        # Vite bundler configuration
```

## Vercel Configuration (`vercel.json`)

To ensure both the frontend and backend operate under the same domain without CORS issues, we use Vercel's rewrite rules defined in `vercel.json`:

1. **API Routes:** Any request to `/api/*` is routed to the Python FastAPI instance located at `/api/index.py`.
2. **Frontend Routes:** All other requests (`/*`) are rewritten to `/index.html`, allowing React Router (if used) or standard React state to handle client-side routing.

## Python API Integration

The `api/` directory utilizes FastAPI running within Vercel's Serverless environment (using Python 3.12).

* **Dependency Management:** Dependencies are managed using `uv`. Before deployment, dependencies are pinned into a `requirements.txt` file which Vercel reads during the build phase.
* **CFD Export Pipeline:**
    * `meshio` is used to convert 3D coordinates into ParaView (`.vtp`) format.
    * `jinja2` dynamically populates OpenFOAM dictionary files (like `blockMeshDict`) which are bundled into a zip archive with the base surface `.stl`.
* **Vercel Limits:** Because Vercel Hobby tier functions have a 10-second timeout, heavy volumetric meshing is *not* performed. The API focuses solely on generating the base geometries and configuration files.