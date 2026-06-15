## 📊 Progress Tracker 

## 👥 Team

| Role | Name | Responsibilities |
|------|------|------------------|
| Analog Front-End (AFE) | Tanmay | 4-ch MUX, PGA, AAF, 12‑bit SAR ADC, Bandgap/PTAT |
| Power Management | Prathmesh | LDOs, wake‑up comparator, power‑gate, reset |
| TinyML Accelerator | Parth | 8‑MAC systolic array, weight streaming, integration with SERV |
| Digital Core & Integration Lead | Neeraj | SERV RISC‑V, SPI, Wishbone bus, top‑level integration, padframe, final DRC/LVS |

## 📝 Weekly Progress Log


| Date | Phase | Progress | Next Steps | Blocker |
|------|-------|----------|------------|---------|
|      |      |          |            |         |


## Legends
Done: ✅
In progress: 🔃


## 🗓️ Chipathon Phases & Tasks

### Phase 1 – Setup & Introduction
- [✅] Install Docker & GF180 PDK for all members
- [🔃] Verify open‑source tool flow (Xschem, ngspice, Magic, Icarus, LibreLane)
- [✅] Study Chipathon block‑B padframe & rules

### Phase 2 – Team Formation & Project Planning
- [🔃] Draft **interface specification** (all signals between analog & digital, power domains)
- [🔃] Agree on **macro area budgets** (e.g., AFE ≤ 0.15 mm², power ≤ 0.1 mm², digital ≤ 0.3 mm²)
- [ ] Create **floorplan sketch** showing macro placement inside pad ring
- [ ] Assign top‑level integration lead (likely Digital Core person)
- [ ] Define power/ground separation (VDD_A, VDD_D, GND_A, GND_D, star‑point)

### Phase 3 – Design & Simulation
- **AFE & Power**
  - [ ] Schematic capture (Xschem)
  - [ ] SPICE simulation of PGA, ADC, wake‑up comparator, LDOs
  - [ ] Verify analog performance (noise, gain, power)
- **Digital Core**
  - [ ] Write Verilog RTL (SERV, SPI master/slave, Wishbone bus, SRAM interface)
  - [ ] Integrate TinyML accelerator Verilog into digital wrapper
  - [ ] Co‑simulate digital core with Icarus Verilog (testbench with ADC model)
  - [ ] Verify SPI communication & interrupt generation
- **Integration testing**
  - [ ] Mixed‑signal functional test plan (e.g., read ADC, run inference, send interrupt)

### Phase 4 – Layout & Verification
- **Analog Macros**
  - [ ] Full‑custom layout (Magic/Klayout) for AFE & Power blocks
  - [ ] Export GDS + port list (or LEF) with coordinates
  - [ ] DRC & LVS on each analog macro
- **Digital Macro**
  - [ ] Synthesise unified digital block (Yosys + OpenROAD via LibreLane)
  - [ ] Generate digital GDS & LEF
  - [ ] Verify timing & area constraints
- **Top‑Level Assembly**
  - [ ] Instantiate all macros (analog & digital) in top‑level layout
  - [ ] Place inside Block‑B padframe
  - [ ] Route inter‑block signals & power nets
  - [ ] Run **DRC** on full chip
  - [ ] Create top‑level Xschem for **LVS** verification (Netgen)
  - [ ] Fix any violations

### Phase 5 – Manufacturing & Testing
- [ ] Final tape‑out GDS submitted to Chipathon
- [ ] Prepare test plan (bring‑up, characterisation, inference demo)
- [ ] (Post‑silicon) Validate sensor readings, wake‑up, stress detection

## 📦 Key Integration Deliverables
- **Interface Specification** (markdown table): signal names, widths, directions, voltage domains
- **Floorplan Diagram** (ASCII or SVG): macro placement within pad ring
- **Analog Macro Port Lists** (text): port name, Metal layer, coordinates
- **Digital LEF** from LibreLane



