# picomatch Dependabot reproduction

This is a minimal public reproduction for a Dependabot security update failure with mixed major versions of `picomatch` in a Yarn 4 lockfile.

## Dependency shape

`package.json` only contains two public dev dependencies:

- `anymatch@3.1.3` -> `picomatch@2.3.1`
- `vite@7.3.1` -> `picomatch@4.0.3`

The important part is that the lockfile contains both:

- a vulnerable `2.x` branch at `2.3.1`
- a vulnerable `4.x` branch at `4.0.3`

## Expected GitHub alerts

GitHub should be able to identify both advisory ranges for `picomatch` from the same lockfile:

- `< 2.3.2`
- `>= 4.0.0 < 4.0.4`

## Expected Dependabot behavior

Dependabot should be able to update the vulnerable `2.x` branch to `2.3.2` and the vulnerable `4.x` branch to `4.0.4`.

## Observed Dependabot behavior in the original private repo

Dependabot may instead fail with a `security_update_not_possible` error similar to:

- latest resolvable version: `2.3.2`
- lowest non-vulnerable version: `4.0.4`
- conflicting dependencies: `[]`

That suggests the updater is conflating the `2.x` and `4.x` branches under the same package name.

## Notes

- The repo intentionally keeps the vulnerable `2.3.1` and `4.0.3` lockfile entries so GitHub can detect the alert.
- `package.json` does not contain a `resolutions` override; the mixed-major state is visible in `yarn.lock`.
- Avoid regenerating the lockfile before testing, or Yarn will resolve the selectors to patched versions and the alerts may disappear.