---
title: Matilda Destination
---

[Matilda](https://app.matilda.io/?utm_source=segmentio&utm_medium=docs&utm_campaign=partners) helps teams manage workspace activity, CRM workflows, and AI-assisted operations in one place. The Matilda destination ingests Segment events into workspace-scoped `appEvents` so teams can monitor activity and trigger downstream actions.

This destination is maintained by Matilda. For any issues with the destination, contact the Matilda Support team at support@matilda.io.

## Getting started

{% include content/connection-modes.md %}

1. From your workspace's [Destination catalog page](https://app.segment.com) search for **Matilda**.
2. Select **Matilda** and click **Add Destination**.
3. Select an existing Source to connect to Matilda.
4. In Matilda, go to **Workspace Settings > Segment** and create or open your workspace Segment connection.
5. Copy your Matilda ingest endpoint:
   - Recommended: `https://api.<stage>.matilda.io/segment-connections/ingest`
   - Alternate (connection-specific): `https://api.<stage>.matilda.io/segment-connections/{connectionId}/ingest`
6. Copy both values from Matilda:
   - **Connection ID** (`connectionId`)
   - **Connection Key** (`writeKey`, used as API key)
7. In Segment destination settings:
   - Set **Destination endpoint** to the Matilda ingest URL.
   - Set **API Key / Write Key** to the Matilda `writeKey`.
   - Set **Connection ID** to the Matilda `connectionId`.
8. Save and enable the destination.
9. Send a test event from Segment Event Tester and verify it appears in Matilda under **Workspace Settings > Segment > Recent Segment Events**.

Data is typically visible in Matilda within seconds of delivery from Segment.

## Destination settings

The Matilda destination uses the following settings:

- **Destination endpoint** (required): Matilda ingest API endpoint.
- **API Key / Write Key** (required): Matilda `writeKey` used to authenticate ingest requests.
- **Connection ID** (required): Matilda `connectionId` used to resolve the workspace connection.

## Supported methods

Matilda supports the following methods, as specified in the [Segment Spec](https://segment.com/docs/connections/spec/).

### Track

Send [Track](https://segment.com/docs/connections/spec/track/) calls to create or update workspace activity in Matilda. For example:

```javascript
analytics.track("contact.created", {
  name: "Jane Doe",
  email: "jane@example.com"
});
```

Matilda ingests Track calls as workspace `appEvents`.

Common mapped event names:

- `contact.created`
- `contact.create`
- `matilda.contact.created`
- `company.created`
- `company.create`
- `matilda.company.created`

If an event name is not currently mapped, Matilda stores it as an ingested event with status `unsupported`, which still appears in workspace activity logs for troubleshooting and rollout planning.

### Identify

Send [Identify](https://segment.com/docs/connections/spec/identify/) calls when you want user context stored alongside activity processing. For example:

```javascript
analytics.identify("user-123", {
  email: "jane@example.com",
  name: "Jane Doe"
});
```

Matilda ingests Identify payloads as `appEvents` and makes them available in activity logs.

### Page

Send [Page](https://segment.com/docs/connections/spec/page/) calls to capture page-level activity context. For example:

```javascript
analytics.page("Pricing");
```

Matilda ingests Page payloads as `appEvents` for workspace activity visibility.

### Screen

Send [Screen](https://segment.com/docs/connections/spec/screen/) calls from mobile flows to capture screen-level context. For example:

```javascript
analytics.screen("Home");
```

Matilda ingests Screen payloads as `appEvents` for workspace activity visibility.

### Group

Send [Group](https://segment.com/docs/connections/spec/group/) calls to associate users with workspace/account context. For example:

```javascript
analytics.group("workspace-456", {
  name: "Acme Inc"
});
```

Matilda ingests Group payloads as `appEvents` for workspace activity visibility.

## How Matilda processes events

For each inbound request, Matilda:

1. Authenticates the request using API key.
2. Resolves the target workspace Segment connection.
3. Normalizes incoming Segment payloads.
4. Applies allowed-event filters configured in Matilda.
5. Stores normalized data in workspace-scoped `appEvents`.
6. Marks processing status as `processed`, `skipped`, `unsupported`, or `failed`.

This destination writes to Matilda `appEvents` (activity stream data) and does not directly mutate core CRM entities unless explicitly enabled by future mapping logic.
