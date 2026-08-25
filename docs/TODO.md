# Docking engineering TODO

## P0 — integration blockers

- [ ] Migrate authority handling to typed `/omni/control/authority`, keeping
  the string topic façade only as a tested compatibility adapter.
- [ ] Fail closed on pose-frame mismatch; migrate `lio_map` configuration to
  `omni_map` with an explicit schema/conversion tool.
- [ ] Gate actions on `/omni/tf_manager/ready` and timestamp-valid
  `omni_map -> omni_base_link` availability.
- [ ] Add ROS launch tests covering QoS, action cancel, lease loss, E-stop and
  forced process shutdown with observed zero velocity.
- [ ] Resolve the extracted-source license conflict and add the authoritative
  root `LICENSE`.

## P1 — docking reliability

- [ ] Add configurable dock sensor/contact input instead of relying only on
  pose and BMS inference.
- [ ] Add obstacle/collision gating for final approach and undock.
- [ ] Calibrate and version model-specific footprint, stopping distance,
  charge-current sign and dock geometry.
- [ ] Add bounded recovery policies that require an explicit caller retry;
  preserve the current no-infinite-retry guarantee.
- [ ] Record approach traces, terminal reason, BMS evidence and timing for
  field diagnosis.
- [ ] Run repeated docking/undocking endurance tests with acceptance metrics
  for success rate, duration, final pose and charge-confirmation latency.

## P2 — productization

- [ ] Evaluate OpenNav Docking plugins behind the same Omni action contract;
  do not expose Nav2-specific interfaces to mission/App layers.
- [ ] Publish signed x86_64, Orin and S100 artifacts and robot-model parameter
  packs.
- [ ] Provide a guided dock-pose capture/validation CLI and operator runbook.
