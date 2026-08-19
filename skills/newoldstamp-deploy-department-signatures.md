---
name: Roll out branded signatures to a department
description: Create a department, build its signature, and deploy it to Google Workspace or Microsoft 365 users.
api: graphql/newoldstamp.graphql
operations: [createDepartment, createSignature, verifyGsuiteDeploy, deployGsuite, verifyOfficeDeploy, deployOffice]
---

# Roll out branded signatures to a department

Newoldstamp's API is GraphQL at `POST https://newoldstamp.com/api/graphql`. This is
the application backend (undocumented, no public developer program); introspection
is open but there is no published API-key surface — obtain a bearer access token
via the `signIn` mutation and send it on every request.

## Auth
1. `signIn(email, password, newGoogleRecaptchaToken)` → `UserAccessToken`. Use its
   access token as a bearer credential. Rotate with `refreshToken(refreshToken)`.

## Steps
1. **Create the department.** `createDepartment(title)` → `Department`. Keep its `id`.
2. **Create a signature for it.** `createSignature(departmentId, json, template, photo, banner, icons, ...)`
   → `Signature`. The `json` field carries the signature layout definition.
3. **Deploy to Google Workspace:**
   - First `verifyGsuiteDeploy(departmentId, data)` to preview/validate the mapping
     (returns `[SignatureForDeploy!]`).
   - Then `deployGsuite(departmentId, data)` to apply.
4. **Deploy to Microsoft 365 / Exchange:**
   - `verifyOfficeDeploy(data)` then `deployOffice(departmentId, data)`.

## Conventions
- Mutations are **not idempotent** — retrying a `create*` creates duplicates. Track
  returned ids and reconcile before retrying (see `conventions/newoldstamp-conventions.yml`).
- Long-running deploys/imports return a `JobNotifier`; watch progress via the
  `jobNotifier` subscription or `jobNotifiers` query.
- Errors surface in the standard GraphQL top-level `errors[]` array.
