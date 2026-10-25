# HUSKYLENS I2C fixes — English, compatible blocks

Based on https://github.com/tangjie133/pxt-huskylens (MIT).

v0.0.3 retains the I2C receive-buffer and timeout fixes and restores the original statement blocks for `initI2c()` and `initMode(mode)`. The Face/Tag/etc. enum selection remains unchanged. These original initialization functions retry until success, yielding between attempts.

For bounded initialization, use the additional advanced boolean blocks `tryInitI2c()` and `tryInitMode(mode)`. Code assigning the old patched initialization return value must migrate to these names:

```typescript
let connected = huskylens.tryInitI2c()
if (connected) connected = huskylens.tryInitMode(protocolAlgorithm.ALGORITHM_TAG_RECOGNITION)
```

`isRequestSuccessful()` reports the latest data-request status. Failed requests clear old results. I2C physical faults, power issues, and firmware stalls require separate diagnosis. Hardware/MakeCode visual validation remains pending.

Install: https://github.com/itibanbosi/pxt-huskylens-fixed#v0.0.3
