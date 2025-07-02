 # API Changes: Tailwind CSS v3 → v4

 This document summarizes the key API additions, changes, and removals when upgrading from Tailwind CSS v3 to v4.

 ## Added in v4.0.0
 - **High-Performance Engine**: New compiler offering up to 5× faster full builds and 100× faster incremental builds.
 - **Modern CSS Features**: Built on cascade layers, registered CSS custom properties with `@property`, and `color-mix()` support.
 - **Zero-Config Installation**: Simplified setup with automatic content file detection and built-in CSS `@import` support.
 - **First-Party Vite Plugin**: Official `@tailwindcss/vite` plugin for seamless Vite integration.
 - **CSS-First Configuration**: Configure and extend Tailwind directly in CSS instead of a JS config file.
 - **CSS Theme Variables**: All design tokens exposed as native CSS variables.
 - **Dynamic Utilities & Variants**: Support for arbitrary values and the new `not-*` variant natively.
 - **Container Queries & 3D Transforms**: New container query utilities and 3D transform support.
 - **Expanded Gradients & Transitions**: Radial/conic gradients, interpolation modes, and `@starting-style` variant.

 ## Changed
 - **@variant renamed to @custom-variant**: The at-rule for custom variants changed name.
 - **Alpha function syntax**: `--alpha(color, 50%)` now uses `--alpha(color / 50%)`.
 - **outline-hidden behavior**: Now sets `outline-style: none` except in forced-colors mode.
 - **--container-prose removed**: Replaced by the deprecated `--max-width-prose` theme variable.
 - **@property fallbacks removed**: No longer emits fallback rules for Firefox.

 ## Removed
 - **IE 11 support**: Dropped the legacy `target` feature.
 - **Legacy shadow utilities**: `shadow-outline`, `shadow-solid`, and `shadow-xs` have been replaced by the Ring API.
 - **negativeMargin plugin**: Removed in favor of negative keys in the standard margin plugin.