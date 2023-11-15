# GitHub Action to run SBT release

Use this to run SBT release process.

You can provide it with a PGP key for signing, in which case, it
should be a base64 encoded GPG key. E.g., the output from something
like this:

```
gpg --export-secret-keys <key-id> | base64
```

By default, this action will generate a snapshot release. To generate
a non snapshot, production release, set `isSnapshot` to `false`.

While running SBT release, this action sets the system property
`RELEASE_TYPE` to either `snapshot` or `production`, depending on the
value of `isSnapshot` in the inputs. This can be useful for
customising the behaviour in the `build.sbt` using a check like this,
for example:

```scala
sys.props.get("RELEASE_TYPE") match {
  case "production" => // ...
  case "snapshot"   => // ...
}
```

Example workflow that uses this action:

```yaml
name: 'Publish Release'
on:
  release:
    types: [published]
jobs:
  sbt_release:
    runs-on: ubuntu-latest
    steps:
      - uses: guardian/actions-sbt-release@v1
        with:
          fetchDepth: 0
          pgpSecret: ${{ secrets.PGP_SECRET }}
          pgpPassphrase: ${{ secrets.PGP_PASSPHRASE }}
          isSnapshot: ${{ github.event.release.prerelease }}
```
