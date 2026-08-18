# Alchemist integration

The `alchemist` branch is the release branch for the optional CorridorKey effect pack used by
[Alchemist](https://github.com/alchemist-editor/native). The `main` branch remains an unmodified
mirror of upstream CorridorKey-Runtime so upstream changes can be audited independently.

## OFX key controls

Both Green and Blue descriptors expose three additional static OFX parameters in the Key Setup
group:

- `key_color` is an OFX RGB value interpreted as sRGB. Green defaults to `(0, 1, 0)` and Blue
  defaults to `(0, 0, 1)`.
- `key_tolerance` is a normalized double with default `0.08`.
- `key_softness` is a normalized double with default `0.12`.

These controls only affect the rough guide generated when no readable Alpha Hint clip is
connected. An external Alpha Hint takes precedence. The selected shade never changes the Screen
Color choice or selects a different model.

The fallback guide normalizes both the input pixel and key color by RGB sum, divides their
Euclidean chromaticity distance by `sqrt(2)`, and applies
`smoothstep(tolerance, tolerance + softness, distance)`. Pixels whose RGB sum is below `0.03`
remain foreground. When the explicit Blue-Green Channel Swap path is active, the picked color is
mapped through the same transform as the source before the guide is generated.

## Release and upstream sync

Alchemist effect-pack releases are built only from reviewed commits on `alchemist`. Release tags
use the form `v<upstream-version>-alchemist.<revision>` and begin with
`v0.7.3-alchemist.1`. Platform bundles continue to use CorridorKey's existing out-of-process
runtime service; Alchemist does not bundle Python.

To synchronize upstream:

1. Fast-forward `main` to the audited upstream commit without Alchemist patches.
2. Create a topic branch from `alchemist` and merge or cherry-pick the upstream range.
3. Resolve the small Alchemist delta in the OFX descriptors, rough guide, and their tests.
4. Run the full CorridorKey verification and performance gates for every supported platform.
5. Open a pull request back to protected `alchemist`; never merge release work directly to
   `main`.

The Alchemist repository owns pack signing, feed metadata, installation, activation, and rollback.
This repository owns the plugin/runtime/model payload and its platform-specific signing inputs.
