# Language

Use these terms exactly — consistent language is the point.

- **Module** — anything with an interface + implementation (function, class, package, slice).
- **Interface** — everything a caller must know: types, invariants, error modes, ordering, config.
- **Depth** — behaviour behind a small interface. Deep = high leverage. Shallow = interface nearly as complex as implementation.
- **Seam** — where behaviour can be altered without editing in place (not "boundary").
- **Adapter** — concrete thing satisfying an interface at a seam.
- **Deletion test** — delete the module. Complexity vanishes? Pass-through. Reappears across callers? Earning its keep.
- One adapter = hypothetical seam. Two adapters = real seam.
