# SurfScout – Spatio‑Temporal Platform for Wind Sports Analysis

SurfScout is a full‑stack, spatio‑temporal analytics platform designed to evaluate wind fields, tidal dynamics, and surf session data.
The system combines geospatial processing, environmental data integration, and predictive modeling to better understand and forecast wind sports conditions.

SurfScout focuses on:

- Wind‑field analysis across space and time
- Session analysis based on geolocation, timestamps, wind, and tide data
- Pattern discovery from historical conditions
- Prediction of future surf suitability
- Robust data and system architecture for long‑term analytics

## 🌊 Project Purpose

Session quality at many spots - especially in spatially constrained coastal regions like the southern North Sea — is difficult to predict.
These areas have a small atmospheric “wave engine”, making it challenging to derive reliable forecasts from conventional weather models.

SurfScout aims to close this gap by:

- systematically collecting surf session quality, wind, and tidal data
- analyzing historical patterns to understand how conditions relate to specific locations
- building a foundation for data‑driven condition predictions
- improving decision‑making for wind‑based water sport sessions

## 🚀 Current Features

- ✔️ User registration & JWT‑based authentication  
- ✔️ Secure password hashing (BCrypt.Net)  
- ✔️ Protected API endpoints using `[Authorize]`  
- ✔️ RESTful API for users, surf sessions, spots and wind field data
- ✔️ PostgreSQL/PostGIS integration via Entity Framework Core
- ✔️ Integration of marine & weather APIs (Stormglass.io, Open‑Meteo)
- ✔️ Historic wind‑field generation from external weather APIs
- ✔️ Angular client with map integration for data visualization

## 🖼️ Screenshot Examples

> ![.](SurfScout_Screen.png)

> _Interpolated wind field within the analysis polygon, with the marker indicating the point where the wind‑speed time series was extracted._

# 🛰️ Tech Stack

### Frontend
- Angular
- MapLibre GL JS
- JSON / GeoJSON communication

### Backend
- ASP.NET Core
- REST API
- JWT Authentication
- NetTopologySuite (geospatial processing)
- TideEngine (internal tide calculation library)

### Database
- PostgreSQL
- PostGIS (spatial extension)

### External Data Sources
- Stormglass.io
- Open‑Meteo

### Infrastructure
- Docker & Docker Compose
- nginx reverse proxy
- Hosted on Webdock VPS

# 🧱 System Architecture

### System Context Diagram

```mermaid
flowchart TB
    User["User / Admin"]
    SurfScout[("surfscout.app - Overall System")]
    OpenMeteo[("OpenMeteo API")]
    Stormglass[("stormglass.io API")]
    TideEngine[("TideEngine - Tide calculation library")]
    LightBM[("LightBM - ML prediction service (future)")]

    User -->|"Uses via browser"| SurfScout
    SurfScout -->|"Fetches weather data"| OpenMeteo
    SurfScout -->|"Fetches tide data"| Stormglass
    SurfScout -->|"Performs tide calculations internally"| TideEngine
    SurfScout -->|"Uses ML predictions"| LightBM
```

### Container Diagram

```mermaid
flowchart LR
    User2["User - Browser"]

    FE["Angular Frontend - Modules: Dashboard, Map, UserConnections, Analytics - Communication: JSON/GeoJSON"]

    BE["ASP.NET Backend - REST API - JWT Auth - NetTopologySuite - TideEngine Library"]

    DB[("PostgreSQL + PostGIS")]

    OpenMeteo2[("OpenMeteo API")]
    Stormglass2[("stormglass.io API")]
    LightBM2[("LightGBM ML Service")]

    NGINX["nginx Reverse Proxy"]

    User2 -->|"HTTPS"| NGINX
    NGINX --> FE
    NGINX --> BE

    FE -->|"REST JSON/GeoJSON"| BE
    BE -->|"SQL Spatial Queries"| DB

    BE -->|"Weather Data"| OpenMeteo2
    BE -->|"Marine Data"| Stormglass2
    BE -->|"Tide Calculation"| BE
    BE -->|"ML Predictions (future)"| LightBM2
```

### Deployment Diagram

```mermaid
flowchart TB

    subgraph Webdock["Webdock VPS"]
        subgraph DockerHost["Docker Host"]

            subgraph Proxy["nginx Container"]
                NGINX2["nginx - TLS termination - Routing"]
            end

            subgraph FrontendC["Frontend Container"]
                FE2["Angular build - served as static files"]
            end

            subgraph BackendC["Backend Container"]
                BE2["ASP.NET Backend - REST API - TideEngine Library"]
            end

            subgraph DBC["PostgreSQL Container"]
                DB2[("PostgreSQL + PostGIS")]
            end

            subgraph MLC["LightGBM Container"]
                LightBM3["Python ML service"]
            end

        end
    end

    OpenMeteo3[("OpenMeteo API")]
    Stormglass3[("stormglass.io API")]

    User3["User Browser"] -->|"HTTPS"| NGINX2
    NGINX2 --> FE2
    NGINX2 --> BE2

    BE2 --> DB2
    BE2 --> OpenMeteo3
    BE2 --> Stormglass3
    BE2 --> LightBM3
```
