# Digital Twin of a Commercial A320 Flight

**Type:** Hobby / passion project
**Pace:** ~2 hours/day, weekdays — no hard deadline
**Goal:** Build a physics-based flight simulator, validate it against a real A320 flight's ADS-B data, and visualize both on a live public dashboard.

This is the actual definition of a digital twin: a physics model *plus* real-world data that the model is checked against and tuned to match — not just a simulator on its own, and not just a data dashboard on its own.

---

## Timeline overview

| Phase | What gets built | Core new skill | Duration @ 2h/day |
|---|---|---|---|
| 0 — Foundations | A "how planes actually fly" write-up, in my own words | Vectors, trig, intuition for derivatives, Newton's laws, the four forces of flight | 10–12 wks |
| 1 — Python for simulation | A projectile-with-drag simulator | numpy, matplotlib, scipy's ODE solver | 4–6 wks |
| 2 — Real flight, watched | A notebook replaying one real Sydney-bound A320 flight | Pulling and parsing real ADS-B data | 3–4 wks |
| 3 — Physics model, built | My own point-mass A320 simulator: takeoff → cruise → landing | Equations of motion + real aircraft performance data | 8–10 wks |
| 4 — The actual twin | Real flight vs. simulated flight, overlaid, with an error score | Model validation | 5–6 wks |
| 6 — Visualization | The public page: 3D flight path + dashboard | Web-based geospatial visualization & charting | 6–8 wks |
| 8 — Polish & writeup | A "how I built this" long-form post | Technical writing | 3–4 wks |
| *5 — Full dynamics (stretch)* | *A properly flyable A320-like model* | *6-DOF flight dynamics, control systems* | *10–12 wks* |
| *7 — Predictive layer (stretch)* | *A small ML model predicting fuel burn from partial trajectory* | *Applying ML to a physics problem* | *4–5 wks* |

**Core path: ~10–12 months. With both stretch phases: ~14–16 months.** Hobby-paced estimates, not deadlines.

Weekly breakdown for Phases 0–2 is in [`study-plan-phases-0-2.pdf`](./digital_twin_study_plan_phases_0-2.pdf) — printed and on the wall.

---

## Phase 0 — Foundations (Weeks 1–11)

Build enough math, physics, and aviation literacy to understand *why* an aircraft behaves the way it does, before writing a line of simulation code.

**Resources:**
- *Stick and Rudder* — Wolfgang Langewiesche (book; read first — almost no equations, all intuition)
- [NASA's Beginner's Guide to Aeronautics](https://www.grc.nasa.gov/www/k-12/airplane/) (free)
- [*See How It Flies*](https://www.av8n.com/how/) — John Denker (free; deeper/more rigorous second pass)
- [3Blue1Brown — Essence of Linear Algebra](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) (free)
- [3Blue1Brown — Essence of Calculus](https://www.youtube.com/playlist?list=PLZHQObOWTQDMsr9K-rj53DwVRMYO3t5Yr) (free)
- Khan Academy — trigonometry & pre-calc refresher

**Milestone:** Publish a "How does a plane actually fly?" post as the first entry in the build log below.

---

## Phase 1 — Python for simulation & data (Weeks 12–16)

Turn Phase 0's math into working code.

**Resources:**
- [Real Python — numpy tutorial](https://realpython.com/numpy-tutorial/)
- [Real Python — matplotlib guide](https://realpython.com/python-matplotlib-guide/)
- [scipy.integrate.solve_ivp docs](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html)

**Mini-project:** A projectile-with-drag simulator — initial velocity + angle in, full trajectory out, with and without drag plotted together.

**Milestone:** Simulator pushed to GitHub as its own small repo.

---

## Phase 2 — Real flight data (Weeks 17–20)

Learn to pull and process a real A320 flight's ADS-B data before building a model to compare against it.

**Resources:**
- [OpenSky Network](https://opensky-network.org/) — free ADS-B data for personal/research use. **Note:** switched to OAuth2 client-credentials auth in March 2026 — older tutorials will show outdated login code.
- [`traffic`](https://traffic-viz.github.io/) — Python library purpose-built for ADS-B/air-traffic analysis, sits on top of OpenSky data

**Milestone:** A notebook replaying one real flight's full altitude/speed/position profile — this exact flight becomes the "ground truth" for Phase 4.

---

## Phase 3 — Physics model, built (Weeks 21–~30)

Build a point-mass (3-DOF) A320 simulator using **real** aircraft performance data instead of guessed numbers.

**Key resource:**
- [OpenAP](https://openap.dev/) — open-source Python library with real A320 mass, thrust, drag, and fuel-flow data (e.g. `openap.prop.aircraft("A320")`). Removes the hardest part of this project: the real performance numbers already exist as open data.
- *See How It Flies* — lift/drag equations, referenced again here
- scipy for solving the equations of motion over a full flight

**Milestone:** A simulated A320 flight (same route as the Phase 2 real flight) generated purely from physics.

---

## Phase 4 — The actual twin (Weeks ~31–36)

Overlay the real flight (Phase 2) against the simulated flight (Phase 3). Quantify the gap — fuel burn, timing, altitude profile — and tune the physics model to close it. **This comparison loop is what makes it a digital twin rather than just a simulator.**

**Milestone:** A "twin accuracy" report/dashboard: simulated vs. real trajectory + error metrics.

---

## Phase 5 — Full 6-DOF dynamics (stretch, optional)

Move from a simple point-mass model to a proper flight dynamics model with rotational dynamics, control surfaces, and autopilot logic.

**Key resource:**
- [JSBSim](https://github.com/JSBSim-Team/jsbsim) — open-source flight dynamics engine (used inside FlightGear). Built entirely from public data, not proprietary Airbus specs. Has a Python module with Colab-ready example simulations.

**Milestone:** Fly a JSBSim A320-like aircraft through a scripted flight plan matching the real route.

---

## Phase 6 — Visualization & web dashboard (Weeks ~31–38, parallelizable)

Build the public-facing page — this is the actual showcase.

**Architecture:** GitHub Pages is static-only (no live Python). So: run the simulation offline/in Colab → export results as JSON → the website reads and renders that JSON. No server needed.

**Tools:**
- [Cesium.js](https://cesium.com/platform/cesiumjs/) — 3D globe/flight-path visualization
- [Plotly.js](https://plotly.com/javascript/) or [Chart.js](https://www.chartjs.org/) — dashboard charts (altitude, speed, fuel burn over time)
- *(Optional, later)* [Pyodide](https://pyodide.org/) — if the simulation should eventually run live in-browser via WebAssembly. Don't reach for this until the offline version works.

**Milestone:** Live interactive page on my GitHub site showing the twin in action.

---

## Phase 7 — Predictive layer (stretch, optional)

Add a small ML model on top — e.g. predict fuel burn or ETA from a partial trajectory, and compare its accuracy to OpenAP's physics-based prediction.

**Milestone:** Short write-up comparing physics-based vs. ML-based prediction accuracy.

---

## Phase 8 — Polish & capstone writeup (Weeks ~39–42)

Documentation pass. Write the full "how I built this" long-form post. Consider presenting at a local meetup.

---

## Build log

*(One entry per milestone — what was tried, what broke, what was learned. Link each entry below as it's published.)*

- [ ] Phase 0 — How does a plane actually fly?
- [ ] Phase 1 — Projectile simulator
- [ ] Phase 2 — A real flight, replayed
- [ ] Phase 3 — A simulated flight, from physics alone
- [ ] Phase 4 — Sim vs. reality: the twin accuracy report
- [ ] Phase 5 — *(stretch)* Full 6-DOF dynamics
- [ ] Phase 6 — The live dashboard
- [ ] Phase 7 — *(stretch)* Predictive layer
- [ ] Phase 8 — Full project writeup

---

## Progress tracking

- **GitHub Projects board** on this repo — one column per phase, cards for sub-tasks.
- **Weekly log, three bullets max** — what moved, what didn't, one line on why. Low bar on purpose, so a bad week still gets logged.
- **Milestone checkboxes above** — check one off as each phase clears.

---

## Community

- **Sydney Aviation Enthusiasts Meetup** (Bankstown) — mixes pilots, non-pilots, and flight-sim enthusiasts; worth checking current activity before relying on it
- Meetup's "Flight Simulator" topic page for Australia — other Sydney groups worth searching for
- **r/flightsim** (Reddit) — active, general flight-sim community
- **`traffic` / OpenSky GitHub Discussions** — for technical questions specific to this exact toolchain
- **VATSIM** — online ATC/flight-sim community, if the "real pilots and controllers" flavor is useful
- If nothing local fits: a small async study group (even 2–3 people, weekly check-ins in a Discord channel) covers the same accountability gap
