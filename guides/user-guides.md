# User Guide Overview

The current documentation is strongest on architecture and implementation detail. This section organizes those materials into operator-facing guidance.

## Main Interfaces

### soca-dashboard

Use these pages when working on a single edge site:

- [Dashboard Design Spec](../soca-dashboard/design-spec.md)
- [Dashboard Edge Operations Plan](../soca-dashboard/edge-operations-plan.md)
- [Dashboard Edge Operations Design](../soca-dashboard/edge-operations-design.md)
- [Dashboard Rule Management Plan](../soca-dashboard/rule-management-plan.md)
- [Dashboard Advanced Rule Management](../soca-dashboard/advanced-rule-management-design.md)

Primary tasks:

- Configure cameras and schedules
- Manage rules and edge behavior
- Push runtime settings to `soca-engine`

### soca-control

Use these pages when working across multiple edges or centralized reporting:

- [Control Design Spec](../soca-control/design-spec.md)
- [Control Edge Operations Plan](../soca-control/edge-operations-plan.md)
- [Control Implementation Plan](../soca-control/implementation-plan.md)
- [Control Pub/Sub Transport](../soca-control/pubsub-transport-plan.md)

Primary tasks:

- Manage fleet-level visibility
- Review alerts and reports
- Coordinate centralized edge operations

## Suggested Next Improvement

To make this section truly user-facing, the next content to add should be task-based walkthroughs:

- Add first camera
- Create first schedule
- Create first detection rule
- Start and stop a job
- Review alerts and reports
- Troubleshoot a failed stream
