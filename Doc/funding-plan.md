# Tether Development Funding Plan: FOSS United Project Grant

**Project:** Tether (with companion layout engine Vinyasa and rendering engine Chitra)  
**Program:** FOSS United Project Grants  
**Maintainer:** Yuvraj Ghoghari (Solo Full-Time Maintainer, India)  
**Grant Duration:** 6 Months (24 Weeks)  
**Total Funding Request:** INR 6,72,000 (INR 6.72 Lakhs)  
**Primary Repository:** [https://github.com/Yuvi-GD/Tether](https://github.com/Yuvi-GD/Tether)  
**Manifest Reference:** [funding.json](funding.json)  

---

## 1. Project Vision & Architectural Context

Modern graphical user interface development spans a broad spectrum of requirements and trade-offs. Webview-based runtimes (such as Electron) and comprehensive object-oriented frameworks (such as Qt) excel at rapid cross-platform application development and rich desktop integration. However, they introduce significant runtime binary footprints, complex dependency chains, and heavy memory baselines. For resource-constrained systems, embedded environments, game developer tooling, and performance-critical desktop utilities, developers require a fundamentally different architectural approach: minimal memory overhead, deterministic execution, zero mandatory runtime dependencies, and seamless integration with native systems code.

**Tether** is an open-source, high-efficiency user interface engine implemented in pure ISO C99 under the Apache-2.0 license. Designed from first principles around Data-Oriented Design (DOD) and an Entity Component System (ECS), Tether operates as an open, extensible UI micro-kernel: **The LLVM of User Interfaces**.

Just as LLVM decouples language frontends (Clang, Rust, Swift) from target machine architectures (x86, ARM, RISC-V) through a common Intermediate Representation (IR), Tether decouples UI authoring frontends (declarative markup, pure C, or high-level scripting languages) from display hardware backends through a format-neutral scene Abstract Syntax Tree (AST) and contiguous ECS memory pools.

Tether encompasses two core companion technologies maintained within its unified architecture:
- **Vinyasa**: An independent, lightweight C layout calculation engine structured around the **Layout Triad** (Primitives, Containers, and Slots), resolving dynamic multi-reference constraints using transient scratch arenas with zero steady-state heap allocations.
- **Chitra**: An independent, hardware-accelerated 2D graphics rendering engine designed around Signed Distance Field (SDF) vector shaders, dynamic font glyph caching, state-batched draw sorting, and multi-backend platform integration.

### The Core Objective: A Universal Substrate for UI Frameworks
Tether is not designed as a closed, end-user application toolkit. Its core goal is to run via native binary or scripting on any machine with low overhead.

Unlike existing toolkits (such as Qt) that force developers to commit to a specific language or make an exclusive choice between native code and scripting, Tether provides complete architectural flexibility. Developers can write pure C, use high-level scripting, or use both simultaneously. Tether allows bi-directional communication between native C systems code and scripting runtimes through lightweight 64-bit scalar entity handles (`Tether_GUID`), without locking developers into proprietary compiler toolchains.

Tether is currently in its early technical preview stage (current: `v0.0.3-preview`), featuring a verified pure C99 ECS kernel, 13 built-in widgets, declarative scene parsing, an interactive desktop Sandbox, 27 automated CTest suites, and automated Windows MSVC CI with AddressSanitizer.

The work funded by this grant is about **improving, modularizing, and extending existing working systems**, rather than rebuilding them from scratch. This proposal funds six months of dedicated full-time maintainer engineering to optimize the core, evolve Vinyasa into an extensible layout engine, integrate SDL3, FreeType/HarfBuzz shaping, and BiDi processing into Chitra, introduce format-independent scene parsing, and build an automated multi-language binding generator with CI merge gates.

---

## 2. Six-Month Milestone Roadmap & Timeline

The six-month engineering schedule is organized into four sequential milestones:
- **Milestone 1 (1.0 Month / Weeks 1 to 4)**: Tether Core: Optimization, Modularity, and Extensibility
- **Milestone 2 (2.0 Months / Weeks 5 to 12)**: Vinyasa: Advanced Modular Layout Engine
- **Milestone 3 (1.5 Months / Weeks 13 to 18)**: Chitra: Advanced Rendering and SDL Integration
- **Milestone 4 (1.5 Months / Weeks 19 to 24)**: Modular Parsing, Custom UI, and Language Bindings

---

### Milestone 1: Tether Core: Optimization, Modularity, and Extensibility
- **Timeline:** Month 1 (Weeks 1 to 4)
- **Duration:** 1.0 Month
- **Estimated Effort:** ~160 maintainer hours
- **Budget Allocation:** INR 1,00,000 (Tranche 1)

#### 1. Existing Foundation:
Tether currently has an operational C99 ECS memory kernel managing generational 64-bit scalar entity handles (`Tether_GUID`), sparse index arrays, and dense component arrays for core properties (layout, shape, typography, visibility, flex slots, state flags, and input routing). A central registry maps string aliases to entities and manages registered widget constructor callbacks. The input subsystem dispatches pointer events via spatial hit-testing with priority routing and reentrant event binding. The baseline passes all 27 automated CTest test suites under AddressSanitizer (ASan) on Windows MSVC.

#### 2. New Engineering Work:
- **Public C API Stabilization**: Clean up public header boundaries, formalize naming conventions and struct alignments, and establish clean, un-mangled C ABI contracts for external consumers.
- **ECS Optimization and Lifecycle Handling**: Streamline component lookup paths and dense array packing. Implement formal entity and component lifecycle states (`mount`, `update`, `unmount`, `destroy`), providing deterministic lifecycle callbacks for subsystems and dynamic components.
- **Modular Memory Management**: Refactor existing memory mechanisms into reusable, standalone C99 modules:
  - Dynamic contiguous arrays with configurable growth and shrink policies.
  - Capacity management utilities and linear frame scratchpad arenas for transient allocations.
  - Reusable container data structures (compact sparse sets, slot maps, handle recyclers) decoupled from specific UI component types.
- **Custom Component and Widget Extensibility**:
  - Preserve and enhance dynamic component registration, adding explicit component destructors, memory layout descriptors, and copy handlers so external frameworks can attach native state to entities.
  - Extend the registry to support registering user-defined widgets, custom event types, and custom subsystem handles without modifying engine internals.
- **Input and Event System Improvements**:
  - Decouple event dispatching so user-defined events integrate seamlessly with the existing event queue.
  - Improve input routing, multi-pointer hit-testing, and event propagation lifecycle.
- **Memory Efficiency and Execution Pipeline**:
  - Enforce a deterministic per-frame execution sequence (Input Normalization -> Layout Resolution -> Visual Synchronization -> Render Submission -> Tag Flushing).
  - Eliminate steady-state heap allocations during frame updates by serving dirty-path evaluations and event payloads from linear scratch memory.
- **Multi-Platform CI Foundation**:
  - Establish a Linux build and automated testing matrix alongside the existing Windows MSVC CI.

#### 3. Deliverables:
- Modular memory management headers and implementations for dynamic arrays, scratch arenas, and handle containers.
- Public C API headers documenting dynamic component registration, custom widget factories, and custom event registration.
- Comprehensive unit test suite covering memory module growth/shrink policies, custom component destruction, and lifecycle callback ordering.
- Benchmark reports demonstrating zero steady-state heap allocations during static frames under the standard benchmark harness.
- Automated Linux CI pipeline running builds and test suites alongside Windows MSVC.

#### 4. Acceptance Criteria:
- Developers can register a custom component with an attached destructor and a custom widget with custom events from an external application without modifying core Tether source files.
- Reusable memory modules pass standalone test suites under AddressSanitizer with zero memory leaks.
- All existing 27 CTest suites continue to pass 100% on both Windows and Linux CI with no regressions.

#### 5. Dependencies and Technical Risks:
- **Dependencies**: Existing Tether C99 codebase.
- **Technical Risks**: Refactoring core memory structures without breaking dense component array alignment or ABI compatibility.
- **Mitigation**: Maintain continuous regression testing against the existing 27 CTest suites and ASan verification across all changes.

#### 6. Estimated Effort and Timeline:
- 1.0 Month (Weeks 1 to 4), ~160 hours.

---

### Milestone 2: Vinyasa: Advanced Modular Layout Engine
- **Timeline:** Months 2 & 3 (Weeks 5 to 12)
- **Duration:** 2.0 Months (Substantial time allocated due to mathematical and algorithmic complexity)
- **Estimated Effort:** ~320 maintainer hours
- **Budget Allocation:** INR 2,00,000 (Tranche 2)

#### 1. Existing Foundation:
Tether currently includes a built-in two-pass layout solver supporting simple flex rows, columns, padding, gaps, and canvas anchors. Vinyasa exists as an independent repository with early prototypes for row layout, core engine definitions, and element sizing.

#### 2. New Engineering Work:
- **Independent Layout Engine Architecture**: Preserve Vinyasa as an independent, general-purpose C layout library that can be used standalone or embedded in other applications, rather than an internal-only Tether subsystem.
- **Primitive, Container, and Slot Model**:
  - **Primitive**: The geometry-owning element maintaining output coordinates (x, y, width, height).
  - **Container**: The rule-based parent behavior that measures and arranges child items.
  - **Slot**: The child settings specifying per-child placement, sizing weight, margins, and alignment overrides within the parent container's layout context.
- **Extensible Measurement and Arrangement Engine**:
  - Implement a generic measurement and arrangement pipeline, allowing developers to define custom container rules and slot properties via clean function-pointer interfaces.
- **Built-in Initial Presets (Flex and Canvas)**:
  - **Flex Container**: Implements directional layout (`ROW`, `COLUMN`), flex grow/shrink factor resolution, cross-axis alignment (`START`, `CENTER`, `END`, `STRETCH`), margins, and gap spacing.
  - **Canvas Container**: Implements multi-anchor responsive docking (`anchor_min`, `anchor_max`, pixel offsets, and pivot points).
  - *Scope Note*: Grid, docking, and other specialized containers are future additions, not required presets for this grant.
- **Nested Layouts, Sizing, Constraints, & Invalidation**:
  - Support arbitrary tree nesting of Flex and Canvas containers with min/max constraint propagation.
  - Implement decoupled intrinsic content measurement (such as text reflow where height depends on available width) via caller callbacks.
  - Implement dirty-subtree invalidation so unchanged branches skip recalculation.
- **Caller-Controlled Memory & Zero Retained State**:
  - Vinyasa maintains no persistent retained scene graph, widget objects, or hidden heap nodes.
  - All intermediate calculations execute in a caller-supplied transient scratch arena, resetting immediately upon pass completion.
- **Tether Integration Adapter**:
  - Implement the Tether layout interface (`tether_layout.h`) and Vinyasa adapter (`vinyasa_layout.c`), bridging Tether's ECS hierarchy and layout dirty tags to Vinyasa's calculation engine.

#### 3. Deliverables:
- Standalone Vinyasa C library with public headers (`vinyasa.h`, `flex.h`, `canvas.h`), build configuration, and standalone test suite.
- Tether layout adapter connecting Tether to Vinyasa.
- Layout test suite covering 60+ geometric test cases: nested flexbox, canvas anchoring, auto-wrapping text constraints, and boundary conditions (zero size, infinite available space).
- Interactive showcase scene in Tether Sandbox demonstrating responsive reflow under live resizing.

#### 4. Acceptance Criteria:
- Developers can implement and register a custom container rule in Vinyasa without modifying Vinyasa's core solver.
- Standalone Vinyasa test suite passes 100% under ASan with zero memory leaks.
- Tether Sandbox executes nested Flex and Canvas layouts through the Vinyasa adapter, achieving the engineering target of sub-millisecond layout calculation for 500+ entities evaluated under the standard benchmark harness on modern desktop hardware.

#### 5. Dependencies and Technical Risks:
- **Dependencies**: Milestone 1 memory modules and component stability.
- **Technical Risks**: Non-linear text height-for-width measurement causing cyclic constraint dependencies.
- **Mitigation**: Strictly bound measurement passes to two passes and standardize the content measurement callback contract.

#### 6. Estimated Effort and Timeline:
- 2.0 Months (Weeks 5 to 12), ~320 hours.

---

### Milestone 3: Chitra: Advanced Rendering and SDL Integration
- **Timeline:** Months 4 to 5.5 (Weeks 13 to 18)
- **Duration:** 1.5 Months
- **Estimated Effort:** ~240 maintainer hours
- **Budget Allocation:** INR 1,50,000 (Tranche 3)

#### 1. Existing Foundation:
Chitra currently functions as Tether's default rendering backend using Sokol GFX (Direct3D 11 on Windows) and Sokol App for windowing. It manages retained GPU shape handles rendered with Signed Distance Field (SDF) shaders for rounded rectangles and borders, dynamic FontStash glyph caching for ASCII/Latin text, hardware scissor clipping, and 64-bit visual key radix sorting.

#### 2. New Engineering Work:
- **SDL3 Backend Support (Committed Priority)**:
  - Implement a robust, cross-platform SDL3 platform and graphics backend alongside the existing Sokol implementation.
  - Support cross-platform window creation, display scale/DPI discovery, and desktop event loop integration on Linux and Windows.
  - **Important Architectural Boundary**: Input and event handling belong to Tether. Chitra will not own or manage Tether's input system. The SDL platform backend translates raw platform events into Tether's normalized event structures, preserving Tether's full ownership of hit-testing, routing, and dispatching.
- **Typography Pipeline: FreeType, HarfBuzz, and BiDi Integration (Committed Priority)**:
  - Integrate FreeType for high-quality font glyph rasterization, sub-pixel positioning, and hinting.
  - Integrate HarfBuzz for complex-script text shaping: support contextual ligature substitution (Devanagari conjuncts, Indic scripts) and mark positioning.
  - Implement bidirectional text processing (Arabic, Hebrew) through a dedicated Unicode BiDi ordering pass (e.g. FriBidi or a dedicated C BiDi implementation) before passing shaped runs to glyph caching.
  - Maintain dynamic glyph atlas caching so shaped text strings avoid redundant rasterization.
- **Expanded Image and Vector Rendering**:
  - Expand vector rendering capabilities: support procedural SDF vector paths, asymmetric per-corner border radii, inner and outer border strokes, and anti-aliased shape outlines.
  - Support textured quads with proper UV clipping and image rendering.
- **Shader-Driven Styling and Visual Effects**:
  - Procedural multi-stop linear and radial color gradients evaluated in fragment shaders.
  - Procedural soft drop shadows and inner shadows without multi-pass blur overhead.
- **Separated Animation Capabilities**:
  - Implement property animation capabilities with clear separation of responsibilities: Tether manages animation timing, state transitions, and property tweening; Chitra receives updated visual parameters (positions, colors, opacity) for hardware submission.
- **Backend Reliability, Portability, and Validation**:
  - Validate rendering output on Linux (OpenGL/SDL3) and Windows (D3D11/SDL3).
  - Establish automated visual regression test harnesses capturing framebuffer snapshots in CI.

#### 3. Deliverables:
- SDL3 backend module supporting Linux and Windows.
- FreeType, HarfBuzz, and BiDi typography module integrated into Chitra.
- Extended SDF shader pipeline supporting asymmetric radii, gradients, and shadows.
- Animation execution system in Tether with standard easing curves.
- Automated visual rendering test suite comparing rendered output against baseline snapshots in CI.

#### 4. Acceptance Criteria:
- Complex Unicode text (including Devanagari ligatures and bidirectional text) renders with correct glyph shaping, directional order, and sub-pixel clarity.
- Tether Sandbox compiles and runs interactively on both Linux and Windows using the SDL3 backend, maintaining equivalent layout and visual behavior within documented tolerances.
- Visual regression tests pass on CI with zero memory leaks under AddressSanitizer.

#### 5. Dependencies and Technical Risks:
- **Dependencies**: SDL3, FreeType 2, and HarfBuzz libraries; Milestone 1 memory foundations.
- **Technical Risks**: Building and linking FreeType and HarfBuzz portably across Windows and Linux CI environments.
- **Mitigation**: Use clean CMake package and submodule discovery with explicit compilation flags.

#### 6. Estimated Effort and Timeline:
- 1.5 Months (Weeks 13 to 18), ~240 hours.

---

### Milestone 4: Modular Parsing, Custom UI, and Language Bindings
- **Timeline:** Months 5.5 to 6 (Weeks 19 to 24)
- **Duration:** 1.5 Months
- **Estimated Effort:** ~240 maintainer hours
- **Budget Allocation:** INR 1,50,000 (Tranche 4)

#### 1. Existing Foundation:
Tether has a declarative YAML scene parser based on libyaml that parses markup into a format-neutral Abstract Syntax Tree (`Tether_ASTNode`: SCALAR, SEQUENCE, MAPPING) and instantiates live ECS entities. The Central Registry provides entity aliasing, and the public C API passes 64-bit scalar entity handles (`Tether_GUID`).

#### 2. New Engineering Work:
- **Decouple Scene Interpretation from YAML**:
  - Formalize the two-tier parsing architecture:
    1. Format-specific frontends parse source text into the intermediate format-neutral AST.
    2. Format-independent semantic scene instantiator evaluates templates, resolves variables, and instantiates live ECS entities.
  - Define a format-independent C interface allowing custom parsers (e.g. JSON, XML, or custom DSLs) to construct Tether scenes by emitting standard AST nodes.
- **User-Defined Components and Widgets in Declarative UI**:
  - Eliminate hardcoded widget switch statements from the scene parser.
  - Enable custom components and custom widgets registered via the Central Registry to be declared and instantiated directly in markup files.
  - Support custom attribute deserialization handlers so user widgets can parse domain-specific properties seamlessly.
- **Parity between Declarative Definitions and Native C APIs**:
  - Ensure supported declarative definitions map deterministically to native C API constructs: supported UI structures expressible in markup can be created via native C calls, and entities instantiated from markup can be inspected, queried, and updated at runtime via C APIs.
- **Automated Multi-Language Binding Generator (Committed Priority)**:
  - Develop an automated binding generation tool for Tether: a standalone generator that inspects Tether's public C99 headers and AST definitions to automatically output foreign language wrappers.
  - Initial supported language targets:
    1. **Python**: Idiomatic Python language bindings utilizing `ctypes`/`cffi`.
    2. **Rust**: Safe, idiomatic Rust language bindings wrapping the C ABI with compile-time type safety.
  - Explicitly enforce memory ownership and lifetime invariants: foreign languages hold scalar `Tether_GUID` handles and never hold raw, unsafe heap pointers to internal ECS memory.
- **Automated CI Integration & Merge Gate**:
  - Integrate the binding generator and multi-language test suites directly into GitHub Actions CI.
  - Enforce a strict merge gate: pull requests targeting `main` must pass automated binding generation and end-to-end multi-language test suites.
- **Tested and Documented Milestone Releases**:
  - Assemble release packages demonstrating the completed milestone capabilities, with documentation, integration guides, and precompiled binaries.

#### 3. Deliverables:
- Format-independent parser abstraction headers and implementations supporting YAML and JSON scene loading.
- Custom declarative tag registration API with example demonstrations.
- Standalone automated multi-language binding generator tool.
- Generated binding packages for Python and Rust with example applications.
- Automated CI workflow executing multi-language binding tests as a required merge gate.
- Tagged milestone releases on GitHub with release notes, precompiled Windows/Linux binaries, and signed SHA-256 checksums.

#### 4. Acceptance Criteria:
- Developers can load a declarative UI file containing a custom user-defined widget, successfully instantiating and configuring it at runtime.
- The automated binding generator produces working Python and Rust bindings that compile and run interactive sample applications.
- CI automatically runs the binding test suites on pull requests and successfully blocks merges if any binding test fails.
- Complete, tested, and documented milestone releases published publicly.

#### 5. Dependencies and Technical Risks:
- **Dependencies**: Milestones 1, 2, and 3 stable public C APIs.
- **Technical Risks**: Keeping generated bindings synchronized with evolving C API signatures.
- **Mitigation**: Generate bindings directly from the C header AST during the build process and enforce CI test gates.

#### 6. Estimated Effort and Timeline:
- 1.5 Months (Weeks 19 to 24), ~240 hours.

---

## 3. Deliverables and Tranche Summary

| Milestone / Item | Duration | Primary Focus | Key Deliverables | Allocation (INR) |
| :--- | :---: | :--- | :--- | :--- |
| **M1** | 1.0 Mo | Core Optimization & Extensibility | Reusable memory modules, ECS lifecycle, custom component/widget registration, Linux CI matrix. | **INR 1,00,000** |
| **M2** | 2.0 Mo | Vinyasa Layout Engine | Primitive-Container-Slot model, Flex and Canvas presets, stateless scratch solver, Tether layout adapter. | **INR 2,00,000** |
| **M3** | 1.5 Mo | Chitra Advanced Rendering | FreeType/HarfBuzz text shaping, BiDi processing, procedural vectors, shaders, animations, SDL3 backend. | **INR 1,50,000** |
| **M4** | 1.5 Mo | Modular Parsing & Bindings | Format-independent parser, automated Python/Rust bindings, CI merge gates, tested milestone releases. | **INR 1,50,000** |
| **AI Tooling** | 6.0 Mo | Dedicated Developer Tooling | Claude Max subscription enabling solo maintainer throughput, static analysis, and automated binding generation. | **INR 72,000** |
| **Total** | **6.0 Mo** | | | **INR 6,72,000** |

*Note: The proposed milestone disbursement schedule is structured around completed milestone deliverables and is subject to mutual agreement and formal grant approval by FOSS United.*

---

## 4. Budget Breakdown & Justification

The total grant budget requested is **INR 6,72,000** for the six-month full-time engineering period, structured into dedicated tooling and milestone-based maintainer compensation.

### Monthly Cost Breakdown

| Budget Line Item | Scope & Purpose | Monthly Rate | Duration | Total (INR) |
| :--- | :--- | :--- | :--- | :--- |
| **Maintainer Compensation** | Full-time systems engineering (Yuvraj Ghoghari): C99 architecture, ECS implementation, layout mathematics, driver integration, automated tooling, testing, and maintenance. | INR 1,00,000 | 6 Months | INR 6,00,000 |
| **Dedicated Development Tooling** | Claude Max / AI engineering subscription: daily pair programming, static analysis, FFI binding generation, test case synthesis, and cross-platform build debugging. | INR 12,000 | 6 Months | INR 72,000 |
| **Total Funding Request** | | | **6 Months** | **INR 6,72,000** |

### Proposed Disbursement Schedule:
Funding is structured into dedicated tooling and four milestone-based maintainer tranches disbursed upon the completion and public verification of each milestone:
- **Dedicated Tooling (Months 1 to 6 Continuous)**: 6.0 months * INR 12,000 = **INR 72,000**
- **Tranche 1 (Month 1.0 - Milestone 1 Completion)**: 1.0 month * INR 1,00,000 = **INR 1,00,000**
- **Tranche 2 (Month 3.0 - Milestone 2 Completion)**: 2.0 months * INR 1,00,000 = **INR 2,00,000**
- **Tranche 3 (Month 4.5 - Milestone 3 Completion)**: 1.5 months * INR 1,00,000 = **INR 1,50,000**
- **Tranche 4 (Month 6.0 - Milestone 4 Completion)**: 1.5 months * INR 1,00,000 = **INR 1,50,000**
- **Total**: **INR 6,72,000**

### Tooling Expense Justification:
Low-level C systems development across custom memory allocators, GPU shaders, layout solvers, font shaping engines, and multi-language FFI bindings is notoriously complex and labor-intensive, typically requiring a multi-engineer team. As a solo maintainer, utilizing advanced AI developer tooling (Claude Max) provides continuous pair programming, rapid test synthesis, cross-platform build triage, and automated binding generation. This dramatically multiplies maintainer throughput, enabling a single engineer to execute this ambitious systems roadmap with high software quality.

---

## 5. Community Impact and Long-Term Sustainability

### Target Audience & Use Cases
Tether targets engineers and teams seeking a high-performance, predictable UI foundation without the baggage of heavy runtime environments:
- **C and C++ Systems Developers**: Requiring a native, pure C99 GUI engine with a clean C ABI that integrates directly into existing build systems without requiring C++ runtime dependencies or external compilers.
- **Game Engine & Tooling Engineers**: Building in-game UI, custom level editors, and telemetry dashboards where predictable frame times, deterministic memory usage, and zero runtime allocations are mandatory.
- **Embedded & Resource-Constrained Environments**: Developing graphical interfaces for industrial controllers, medical devices, and embedded Linux hardware where RAM is severely limited and multi-hundred-megabyte webview bundles are infeasible.
- **Desktop Utility Authors**: Creating lightweight, fast-starting desktop applications where sub-second startup times and minimal memory footprints (<10 MB) provide superior user experiences over Electron or Chromium wrappers.

### Architectural Differentiation
Tether occupies a unique architectural position compared to existing open-source UI libraries and frameworks:

- **Tether vs. Qt**:
  - *Qt*: A large, monolithic C++ object-oriented framework relying heavily on deep inheritance hierarchies, the `QObject` meta-object compiler (MOC), virtual method tables (vtables), and large dynamic library footprints (often exceeding 50 MB to 100 MB), forcing developers to choose between C++ and QML.
  - *Tether*: Pure ISO C99 built on a Data-Oriented Entity Component System. Entities are lightweight 64-bit scalar handles (`Tether_GUID`), components reside in contiguous flat memory arrays, and logic operates via linear cache-friendly iteration with zero vtables. Tether allows running via script, binary, or both with bi-directional communication, giving developers total flexibility without runtime lock-in.

- **Tether vs. Dear ImGui**:
  - *Dear ImGui*: An immediate-mode GUI library tailored for developer debugging and internal tools. It evaluates UI logic and re-submits complete geometric vertex buffers every frame regardless of whether the UI state changed, resulting in continuous CPU overhead.
  - *Tether*: A retained-mode ECS engine supporting declarative markup, hierarchical layout calculation with dirty-subtree caching, and hardware-accelerated retained batching. Geometry is only updated when dirty flags are set, dramatically reducing per-frame CPU load for complex consumer and desktop application workflows.

- **Tether vs. Slint**:
  - *Slint*: Employs a custom domain-specific markup language compiled via a specialized Rust-based compiler into native code, requiring developers to adopt a specialized DSL toolchain.
  - *Tether*: Built on standard ISO C99 with format-agnostic scene parsing (supporting standard YAML, JSON, or custom AST emitters). It offers first-class multi-language bindings (Python, Rust) holding lightweight scalar handles, eliminating the requirement for proprietary language compilers.

### Current Status & Early Exploration
Tether is currently in early technical preview (`v0.0.3-preview`) with zero real-world production adoption. The codebase has working public documentation, an interactive desktop sandbox, and 27 automated unit test suites under Windows MSVC.

External engagement is in its earliest exploratory stage, with initial repository stars from open-source graphics engineers (including the creator of ThorVG). The grant provides the dedicated engineering runway needed to advance Tether from an experimental preview into a complete, usable UI substrate.

### Post-Grant Adoption & Sustainability
The six-month grant period is dedicated to building, stabilizing, and testing the core foundation. Post-grant is the period where external adoption realistically begins:
- **External Framework & Application Adoption**: With the core ECS, Vinyasa layout, Chitra rendering, and multi-language bindings completed and verified, third-party developers can begin adopting Tether to build UI frameworks, embedded interfaces, and desktop applications.
- **Package Manager Distribution**: Official distribution via standard package ecosystems (vcpkg and Conan for C/C++; PyPI for Python; crates.io for Rust) will make integration straightforward for developers across diverse language stacks.
- **Decoupled Architecture & Community Contributions**: Tether's decoupled architecture (independent Vinyasa layout rules, format-independent AST parsers, custom widgets) allows external contributors to build and share extensions, backends, and container rules independently without central bottlenecking.
- **Automated CI Maintenance**: Automated multi-platform CI matrices, visual regression suites, and binding merge gates established during the grant will keep the codebase healthy with minimal ongoing maintainer overhead.

---

## 6. Open Source Commitment & Community Accountability

- **Permissive Open Source**: All source code, tools, generators, tests, and documentation produced under this grant are released under the **Apache License 2.0**.
- **Public Repositories**: All development takes place openly on GitHub across public repositories ([Tether](https://github.com/Yuvi-GD/Tether), [Chitra](https://github.com/Yuvi-GD/Chitra), [Vinyasa](https://github.com/Yuvi-GD/vinyasa)).
- **Transparent Progress**: Development updates, pull requests, milestone releases, and interactive demonstrations will be published openly on GitHub and shared with the FOSS United community.
- **Strict Quality Control**: Automated CI pipelines run multi-platform builds (Windows and Linux), memory sanitizers (AddressSanitizer), visual regression tests, and automated language binding merge gates, ensuring continuous stability and zero regressions.
