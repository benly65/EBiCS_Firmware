## Torque sensor override vs throttle override (NG12FET)

This firmware allows selecting the control input source at compile time:

- **Throttle override** (default): uses analog throttle input
- **Torque override**: uses torque sensor (e.g. bottom bracket torque sensor)

### Configuration

Edit `Inc/main.h`:

```c
// Uncomment to enable torque sensor override
#define TORQUE_OVERRIDE