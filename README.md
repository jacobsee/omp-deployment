# Open Management Portal - Deployment & Lifecycle

This repository both bootstraps and manages the deployment lifecycle of the Open Management Portal. It is meant to be used with Argo CD.

## Bootstrapping

To create an instance of the Open Management Portal in a cluster, apply the `bootstrap` Helm chart using the following command:

```sh
helm install open-management-portal bootstrap/ --set application.ref=<desired git ref>
```

Make sure to replace `<desired-git-ref>` with the tag that you want to deploy to this cluster. If this is a staging cluster, you might have a Git tag such as `deploy-staging`; and if this is a production cluster, maybe `deploy-production`.

## Lifecycle Management

The Open Management Portal is not a single application, but a collection of components (or services) that all add up to a working system. Those services might be versioned separately from one another. Therefore, a "deployment of OMP" is really a manifest of the versions of its constituent components that are known to work well with each other.

This manifest lives in `applications/values.yaml`:

```yaml
applications:
- name: omp-frontend
  url: https://github.com/rht-labs/open-management-portal-frontend.git
  ref: master
- name: omp-backend
  url: https://github.com/rht-labs/open-management-portal-backend.git
  ref: master
- name: git-api
  url: https://github.com/rht-labs/open-management-portal-git-api.git
  ref: master
```

The `ref` field for each application refers to a git tag for a version of that application.

Automatic syncing, pruning, and self healing is enabled for this project - so committing a change to the above manifest and tagging it appropriately as `deploy-staging` or `deploy-production` will cause an Argo CD instance bootstrapped with that tag to deploy those versions of the applications.
