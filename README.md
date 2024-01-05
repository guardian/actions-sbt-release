## This Action is deprecated, see [`gha-scala-library-release-workflow`](https://github.com/guardian/gha-scala-library-release-workflow)

[**`gha-scala-library-release-workflow`**](https://github.com/guardian/gha-scala-library-release-workflow)
is the recommended alternative to `guardian/actions-sbt-release`.

As a [reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows#overview),
rather than a GitHub Action, it's able to isolate release phases in
[jobs](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow),
providing better [release credential security](https://github.com/guardian/gha-scala-library-release-workflow/blob/main/docs/security-design.md).

See these PRs as examples of how to switch from `guardian/actions-sbt-release` to `gha-scala-library-release-workflow`:

* https://github.com/guardian/fezziwig/pull/45
* https://github.com/guardian/atom-maker/pull/93

#### GitHub Action to run SBT release

Use this to run SBT release process.

You can provide it with a PGP key for signing, in which case, it
should be a base64 encoded GPG key. E.g., the output from something
like this:

```
gpg --export-secret-keys <key-id> | base64
```

By default, this action will generate a snapshot release. To generate
a non snapshot, production release, set `isSnapshot` to `false` (see 
the example below for how to do this automatically from a release).

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
      - uses: guardian/actions-sbt-release@v3
        with:
          pgpSecret: ${{ secrets.PGP_SECRET }}
          pgpPassphrase: ${{ secrets.PGP_PASSPHRASE }}
          sonatypeUsername: ${{ secrets.SONATYPE_USERNAME }}
          sonatypePassword: ${{ secrets.SONATYPE_PASSWORD }}
          isSnapshot: ${{ github.event.release.prerelease }}
```
