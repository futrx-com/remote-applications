# Remote Applications

Reviewed applications for [remote.futrx](https://github.com/futrx-com/remote.futrx).

## Submit an application

1. Fork this repository.
2. Add your application to `applications/<first letter>/<id>/`, for example
   `applications/h/hello-remote/`. The format is documented in
   [installable applications](https://github.com/futrx-com/remote.futrx/tree/main/docs/dev/installable-applications).
3. Open a pull request. Change one application per pull request.

Every application here is licensed under [AGPL-3.0-or-later](LICENSE), the
license of Remote. Submitting one means you license it the same way.

## Update an application

Change its files and bump `version` in `application.json`. A published version
cannot be changed.

## Check locally (optional)

Pull requests are checked automatically. To run the same checks first (needs Go
and Node.js):

```sh
git clone https://github.com/futrx-com/remote.futrx .core
tools/marketplace check <id>
```

## After you submit

A reviewer reads your pull request. Once it is merged, it is published as a
release of this repository that Remote administrators can install.
