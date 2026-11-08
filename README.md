# Remote Applications

The reviewed marketplace of installable applications for
[remote.futrx](https://github.com/futrx-com/remote.futrx). Every published
version is reviewed, built by CI and listed in a signed index that Remote
servers read.

## Layout

```
applications/<first letter>/<id>/   the application, exactly as Remote loads it
listings/<first letter>/<id>/       marketplace metadata (listing.json), never packaged
tools/marketplace                   validation, packaging and publishing
keys/index-signing.pub              public key that verifies index.json
```

An application is the same directory Remote's own `applications/<id>/` holds.
The format is documented in
[installable applications](https://github.com/futrx-com/remote.futrx/tree/main/docs/dev/installable-applications).

`listing.json`:

```json
{
  "id": "hello-remote",
  "publisher": "futrx",
  "maintainers": ["github-handle"],
  "license": "AGPL-3.0-or-later",
  "minRemote": "0.35.0"
}
```

`maintainers` are the GitHub accounts allowed to propose changes to the
application. Empty means marketplace reviewers maintain it.

## Submitting

1. Fork this repository and add or change one application and its listing.
2. Bump `version` in `application.json` whenever anything under `applications/`
   changes. A published version is never rebuilt.
3. Run `CORE=<remote.futrx checkout> tools/marketplace check <id>`.
4. Open a pull request. One application per pull request.

## Review and release

- A pull request merges only with approval from `@futrx-com/marketplace-reviewers`.
- Each merge that adds an unpublished `<id>@<version>` starts the release workflow.
  It validates and packages the application, then waits for approval on the
  `marketplace-release` environment.
- Once approved, it creates the release `<id>@<version>` with the zip, adds the
  version to `index.json`, signs it, and uploads both to the `index` release:

  ```
  https://github.com/futrx-com/remote-applications/releases/download/index/index.json
  https://github.com/futrx-com/remote-applications/releases/download/index/index.json.sig
  ```

- A release can also be started by hand: **Actions → release → Run workflow**.

`index.json.sig` is a base64 Ed25519 signature over the exact bytes of
`index.json`. Until Remote reads the index, an administrator can upload a
release zip in **Settings → Applications**.

## One-time setup

1. Create the teams `marketplace-reviewers` and `marketplace-admins`.
2. Protect `main`: pull requests only, one code-owner approval, dismiss stale
   approvals, require approval of the latest push, require the `validate`
   check, no bypass.
3. Create the `marketplace-release` environment: required reviewers, prevent
   self-review, deployment branch `main` only.
4. Generate the signing key and keep the private key out of the repository:

   ```sh
   openssl genpkey -algorithm ed25519 -out index-signing.pem
   openssl pkey -in index-signing.pem -pubout -out keys/index-signing.pub
   ```

   Commit `keys/index-signing.pub`. Store the contents of `index-signing.pem`
   as the `INDEX_SIGNING_KEY` secret of the `marketplace-release` environment,
   then delete the file.

## License

[AGPL-3.0-or-later](LICENSE). Each application declares its own license in
its listing.
