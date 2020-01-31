# Versioning

Another advantage of GQL is that the need for versioning largely does away; the schema evolves overtime w/out the need for strict versions.

### Backwards Compatible Changes

99% of GQL changes are backwards compatible; they involve deprecating a field which just results in a warning if used but doesn't stop working. Or it's a new field altogether and since you aren't asking for it, it doesn't affect you.

### Breaking Changes

We do everything possible to avoid these, and at the time of writing this, see no reason why we'll ever have them. That said, let's say a field has been deprecated for 6 months and an integration continues to use it even after being told that it's going away, eventually that field will have to go, which yes, would be a breaking change. Beyond this we don't see a reason why we can evolve this API through clean deprecations.
