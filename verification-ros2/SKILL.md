---
name: verification-ros2
description: Defines what "verified" means for ROS2 development in Python and/or C++ — colcon build across the whole workspace, a three-tier test model (unit tests, ros2 launch integration, robot/HIL-in-loop), and ROS-specific silent-failure traps like QoS mismatches, stale generated interfaces, and lifecycle transitions. Use this whenever a change touches a ROS2 node, topic, service, action, launch file, or custom .msg/.srv/.action interface, in either language.
---

# ROS2 Verification (Python + C++)

Builds cleanly across both language stacks — `colcon build` succeeding for the whole workspace,
not just the package/language you touched. A C++ node change can silently break a Python consumer
of the same interface, and vice versa.

## Tiers

Success is verified in tiers, similar to embedded — a node passing its own unit tests is not proof
the system behaves correctly:

1. **Unit tests (gtest for C++, pytest for Python)** — logic tested with pub/sub and service calls
   mocked out. Catches logic bugs, has no insight into real message flow or timing.
2. **Integration test via actual `ros2 launch`** — nodes actually running, verified with
   `ros2 topic echo` / `ros2 service call` / `ros2 topic hz`, not just mocked interfaces.
3. **Robot/HIL-in-loop** — only tier that confirms real sensor/actuator timing and behavior under
   actual hardware load.

A change isn't verified until it reaches the tier relevant to what it touches — pure logic changes
may stop at (1), anything touching topics/services/timing should reach (2), anything touching
actuation or sensor drivers should reach (3).

## Requirements

- State QoS profile assumptions explicitly (reliability, durability, history depth) before
  changing a publisher or subscriber — a QoS mismatch fails silently at the DDS layer with no
  error, so this needs to be reasoned about up front, not debugged after the fact.
- When a change touches a custom `.msg`/`.srv`/`.action` interface, regenerate and rebuild before
  reasoning about either language's side of it — don't assume the old generated bindings are
  still valid.
- No silent-failure shortcuts for the sake of brevity — e.g. swallowing an error return code to
  keep code short is unacceptable even where it would "simplify" the diff.
- Node lifecycle assumptions (if using lifecycle nodes) — current state, valid transitions — are
  stated explicitly, since an invalid transition fails in ways that are easy to miss in a quick
  read of the diff.
