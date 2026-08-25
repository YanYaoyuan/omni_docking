# Docking interface contract

## Responsibility boundary

`omni_docking` owns dock configuration lookup, final approach, undock motion
and charge verification. It does not plan the global return path, arbitrate
between navigation/teleoperation/docking, publish the final SDK command or
define robot sensor TF.

## Provided interfaces

| Kind | Default name | Type | Semantics |
|---|---|---|---|
| Action | `/omni/docking/dock` | `Dock` | Acquire DOCKING authority, servo, verify charge |
| Action | `/omni/docking/undock` | `Undock` | Acquire DOCKING authority and reverse to standoff |
| Service | `/omni/docking/verify_charge` | `VerifyCharge` | Fresh BMS-based charging verdict |
| Service | `/omni/docking/config` | `GetDockConfig` | Resolve map/version to dock geometry |
| Topic | `/omni/docking/status` | `DockStatus` | Reliable/transient-local state at 1 Hz |
| Topic | `/omni/cmd_vel/docking` | `geometry_msgs/Twist` | Docking-only velocity, 20 Hz while active |

`/omni/cmd_vel/docking` is an input to the unified bridge arbiter. It must
never be remapped directly to a vendor SDK command topic.

## Consumed interfaces

| Kind | Default name | Type | Required behavior |
|---|---|---|---|
| Topic | `/omni/robot_state` | `RobotState` | Fresh localization, E-stop and robot state gate |
| Topic | `/state_estimation_global` | `nav_msgs/Odometry` | Fresh pose in configured map frame |
| Topic | `/battery_state` | `sensor_msgs/BatteryState` | Fresh BMS charge evidence |
| Topic | `/rosdeck/control_status` | `std_msgs/String` | Legacy authority result/heartbeat state |
| Topic | `/rosdeck/control_command` | `std_msgs/String` | Legacy acquire/heartbeat/release command output |

All custom action/service/message types are supplied by
`omni_robot_interfaces`.

## Authority safety contract

The current implementation uses the legacy payload
`<acquire|heartbeat|release>:<client_id>`. The target contract is the typed
`/omni/control/authority` `ControlAuthority` service provided by
`omni_robot_bridge`.

During migration:

1. the bridge must expose typed and legacy façades over one internal lease
   state machine;
2. a request seen on both façades must not create two owners or renewals;
3. authority loss, stale state, E-stop, cancel and process shutdown must
   synchronously publish zero before release;
4. after all clients migrate and soak tests pass, the string topics can be
   deprecated, then removed in a major version.

## Frame contract

The target pose frame is `omni_map`, with robot pose resolved through
`omni_map -> omni_odom -> omni_base_link`. The current default `lio_map` and
`/state_estimation_global` are compatibility values. Dock JSON must record its
frame/schema version; mixing a legacy dock pose with an `omni_map` pose must
fail closed rather than warn and continue.
