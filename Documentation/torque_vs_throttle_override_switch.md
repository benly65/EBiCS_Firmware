# Torque vs Throttle Override Switch

## Important prerequisite (Java Configurator)

⚠️ **All Torque vs Throttle Override related logic is only active if
the _Throttle Override_ checkbox is ENABLED in the Java Configurator.**

This means:

- The Java Configurator option **Throttle Override** must be **ON**
- If it is **OFF**, none of the firmware logic described below is used
- In that case, the firmware behaves like a classic non-override build,
  regardless of `TORQUE_OVERRIDE` or related defines in the source code

The firmware-side switch only selects **which signal feeds the override logic**  
(throttle ADC vs torque sensor ADC), but **does not enable override by itself**.


## Purpose

This commit adds a compile-time switch to select which ADC input controls
the motor current target:

- classic throttle (ADC1)
- torque sensor (ADC6)

No runtime logic, EEPROM layout, or startup calibration is affected.

---

## Configuration

The selection is done in `Inc/main.h`.

### Enable torque override
```c
#define TORQUE_OVERRIDE