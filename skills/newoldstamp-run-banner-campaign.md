---
name: Run a signature banner marketing campaign
description: Upload a banner and run a time-bound marketing campaign across departments' email signatures.
api: graphql/newoldstamp.graphql
operations: [uploadBanner, createCampaign, updateCampaign, completeCampaign, campaigns]
---

# Run a signature banner marketing campaign

Newoldstamp's API is GraphQL at `POST https://newoldstamp.com/api/graphql`. Send a
bearer access token from the `signIn` mutation on every request.

## Steps
1. **Upload the banner image.** `uploadBanner(file)` → `Banner` (multipart upload; the
   `file` argument is a GraphQL `Upload`).
2. **Create the campaign.** `createCampaign(name, startDate, endDate, departmentIds, url, styles, banner)`
   → `Campaign`. `departmentIds` targets which departments' signatures show the banner;
   `url` is the click destination.
3. **Adjust while live.** `updateCampaign(id, name, startDate, endDate, departmentIds, url, styles, banner)`.
4. **List / report.** `campaigns(fromDate, toDate, timePeriodStart, timePeriodEnd)` returns
   campaigns with their analytics window.
5. **End it.** `completeCampaign(id, status)` marks the campaign complete.

## Conventions
- Dates are `ISO8601DateTime`.
- Mutations are not idempotent — capture the returned `Campaign.id` and avoid blind retries.
- See `conventions/newoldstamp-conventions.yml` for pagination and error semantics.
