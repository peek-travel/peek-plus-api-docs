# Versioning

Another advantage of GraphQL is that the need for versioning largely goes away; the schema evolves overtime without the need for strict versions.

### Backwards Compatible Changes

99% of our GraphQL changes are backwards compatible; they involve deprecating a field which just results in a warning if used but doesn't stop working. Or it's a new field altogether and since you aren't asking for it, it doesn't affect you.

![image](https://user-images.githubusercontent.com/1004167/73579222-af686a80-4436-11ea-90ab-ab8e9a242606.png)

### Breaking Changes

We do everything possible to avoid these, and at the time of writing this, see no reason why we'll ever have them. That said, let's say a field has been deprecated for 6 months and an integration continues to use it even after being told that it's going away, eventually that field will have to go, which yes, would be a breaking change. Beyond this we don't see a reason why we can't evolve this API through clean deprecations.
