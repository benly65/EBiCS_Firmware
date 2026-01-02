# Torque offset: ADC stabilisation before averaging (MP.torque_offset)

## What was changed

Previously the firmware used a blind startup delay before averaging the torque sensor offset:

```c
HAL_Delay(...);
for (...) {
    while (!ui8_adc_regular_flag) {}
    ...
}
```

This commit replaces the blind delay with an adaptive method:
the firmware monitors the torque sensor ADC signal after power-up and starts averaging
`MP.torque_offset` only after the signal becomes stable.

This avoids calculating the torque zero offset while the ADC signal is still drifting
or affected by power-up transients.

---

## TL;DR for builders — quick tuning guide

**Goal:** wait until the torque sensor ADC signal is stable after power-up, then calculate
`MP.torque_offset`.

### Default values (recommended)

```c
#define TORQUE_STAB_RANGE_THR        12u
#define TORQUE_STAB_WINDOW_SAMPLES   64u
#define TORQUE_STAB_CONSEC_WINDOWS   4u
#define TORQUE_STAB_TIMEOUT_MS       3000u
```

### What to change if something is wrong

- **Startup too slow?**
  - Reduce `TORQUE_STAB_WINDOW_SAMPLES` (64 → 32), or
  - reduce `TORQUE_STAB_CONSEC_WINDOWS` (4 → 3)

- **Torque zero unstable / jumps?**
  - Increase `TORQUE_STAB_RANGE_THR` (12 → 20), or
  - increase `TORQUE_STAB_CONSEC_WINDOWS` (4 → 6)

- **Very noisy sensor / long cables?**
  - `TORQUE_STAB_RANGE_THR = 20…40`
  - `TORQUE_STAB_CONSEC_WINDOWS = 6…8`

- **Never stabilises?**
  - `TORQUE_STAB_RANGE_THR` is too small → increase it
  - Check sensor wiring and ground
  - Startup will still continue after timeout

### Notes

- All parameters operate on raw ADC counts (before filtering or scaling).
- Torque ADC channel depends on hardware configuration (`TQONAD1`, `adcData[6]` or `adcData[1]`).
- Do not disable the timeout — it prevents startup lock-up if the sensor is faulty or very noisy.
