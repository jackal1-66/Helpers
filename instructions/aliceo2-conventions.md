---
applyTo: "**/AliceO2/**,**/Detectors/**,**/DataFormats/**,**/Framework/**,**/GPU/**,**/Steer/**,**/Simulation/**"
---

# AliceO2 conventions

- No raw owning pointers; use `std::unique_ptr` / `gsl::span`.
- DataHeader + DataProcessingHeader used for all DPL messages.
- GPU code (`GPU/` subtree): follow GPUCommonDef, no STL in device code.
- Detector namespaces: `o2::<det>::` (e.g. `o2::tpc::`, `o2::its::`).
- New data types registered in `DataFormats/` and added to LinkDef if ROOT-serialised.
- Unit tests expected for new algorithms under `<module>/test/`.
