# Future AI Playground Integration Strategies

This document details integration strategies for incorporating the Interactive Mechanics Playground into the author's personal website under the planned **AI Playground** section in the future.

> [!IMPORTANT]
> This integration is **strictly out of scope** for initial development (v1.0.0). The personal website repository must not be accessed, edited, or modified during this project.

---

## 1. Architectural Readiness for Future Integration

To ensure seamless future integration without rework, the application adheres to these constraints:
1. **Configurable Base URLs**: The frontend communicates with the backend via `VITE_API_URL` rather than hardcoded URLs.
2. **CORS Flexibility**: The FastAPI backend accepts configurable allowed origins via `CORS_ORIGINS`.
3. **Self-Contained Components**: Frontend workspace and chart components are modular and decoupled from the top-level page wrapper.
4. **Deterministic Mechanics API**: The backend API provides structured, serializable JSON results ideal for programmatic consumption by a future explanatory AI layer.

---

## 2. Potential Integration Strategies

### Strategy A: Independent Deployment with Direct Link / Project Card
- **Description**: Deploy the playground as an independent web application (e.g. `https://mechanics.example.com`). The personal website features a rich project card with an interactive preview card, summary, tags, and a direct link.
- **Advantages**: Complete architectural independence, zero styling conflicts, independent deployment pipelines, simplest maintenance.

### Strategy B: Sandboxed Responsive `<iframe>` Embed
- **Description**: Embed the independently hosted playground inside an `<iframe>` container on the personal website's AI Playground page.
- **Requirements**: Responsive design with clean viewport bounds; parent frame communication via `postMessage` if bidirectional state sharing is needed.

### Strategy C: Reusable Component Package / Micro-Frontend
- **Description**: Publish `@mechanics-playground/core` or bundle the React components as a library consumed directly by the personal website's build system.
- **Tradeoffs**: Tighter visual cohesion with personal website styles, but couples dependencies and React versions.

---

## 3. Future AI Explanation Layer (Conceptual Architecture)

When AI capabilities are introduced in a future version:
1. **Deterministic Authority**: The structural finite element solver computes all reactions, diagrams, and stresses first.
2. **Context Assembly**: The deterministic results (extrema, curvature signs, reaction equilibrium) are passed to an LLM service as structured JSON facts.
3. **Educational Insights**: The LLM generates natural language explanations (e.g. explaining why moment is quadratic under uniform load or why deflection is zero at supports) based exclusively on the solver's output.
