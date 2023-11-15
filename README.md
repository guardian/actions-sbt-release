# GitHub Action to run SBT release

Use this to run SBT release process.

You can provide it with a PGP key for signing. You should also send it a version to use.

For example:

```yaml
# ...
runs:
  using: guardian/actions-sbt-release@latest
```
