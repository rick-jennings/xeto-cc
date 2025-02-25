# Acuvim II -

## Initial focus of the example

The initial focus is to create a Xeto device spec for the Acuvim II Series Power Meter.  Specifically Stage 1 focuses on the meter's "real-time measurements":

 * BACnet: 71 BACnet objects defined in Table 6-85
 * Modbus: Associated data points defined in Tables 6.3.7, 6.3.8, & 6.3.10

Note: Do not confuse Acuvim's "real-time measurements" with the 50ms or 100ms metering parameters (which are even faster and outside of Stage 1 of this initiative).

Link to the Acuvim II documentation:
https://accucdn.accuenergy.com/wp-content/uploads/Acuvim-II-Power-Meter-User-Manual-1040E1303.pdf

## Meter's configuration settings

 * For modbus we assume the "Primary Mode" for "Parameter Mode" (by default "Secondary Mode" is applied instead)
 * pfStandard => VAR/PF convention: IEC or IEEE
 * pfScope => VAR calculation method: true (for example)

Should we apply these within the individual point specs or higher up the meter spec hierarchy?

Note:  By default the Accuenergy seems to use IEC convention for pf.

FYI, there are other essential settings.  This is just a good starting point.

## Considering how the meter was installed

In Section 2.3.2 of the device manual, Accuenergy defines wiring configs:

  * 1LN
  * 1LL
  * 2LL
  * 3LL
  * 3LN-2.5
  * 3LN

Should we model these configs in Haystack?

In some cases there will be different specs depending on the wiring config.  So there is a base spec for the meter which has points for various wiring configs.

## Energy data points in modbus

They support R/W.  See Table 6-20.  Why is this?

For now using read only access.

## Clarification needed in ph.points & ph.points.elec

### Support for "total" in a direction of electricity flow context

These are the options for the direction of electricity flow that require support:
 * import: measured value flowing to the load from the grid or source (already modeled)
 * export: measured value flowing to the grid or source from the load (already modeled)
 * total: absolute sum of import & export measured values (needs to be modeled)
 * net: algebriac sum of import & export measured values (already modeled)

Note:  The "total" tag in Haystack is already reserved for describing the location of
an electricity measurement.

### Removing direction of electricity flow for AC electric apparent power sensors

According to the math equation below, AC electric apparent power shall always be positive. This means that there should be no `net`, `export`, or `import` distinction for AC electric apparent power.

```math
Apparent Power = \sqrt{(Active Power)^2 + (Reactive Power) ^2}
```

Note:  `net`, `export`, `import`, and `total` apply to AC electric active and reactive power.

### Removing direction for AC electric RMS current sensors

AC electric current oscillates like a sinusoidal waveform.  An electric RMS current data point is associated with the peak value of this waveform (which is greater than zero) divided by the square root of 2.  In other words, the AC electric RMS current shall always be positive and there should be no `net`, `export`, or `import` distinction for AC electric RMS current.

Note: There should be a constraint that reflects that AC electric RMS current and voltage must always be >= 0.0.

### Removing direction related tags for AC electric power sensors

AC electric power can be negative or positive.  However, this does not mean there needs to be `import`, `export`, and `net` variants.  It seems in practice the meter OEMs use a single data point that can be negative or positive.  I am starting to think the `import`, `export`, and `net` tags might only apply to `demand` and `energy`.  This concepts needs to be confirmed.

If confirmed we should:
 - Remove `import`, `export`, and `net` variants for AC electric power sensors
 - Introduce constraints for whether the value should be negative, positive, or both

### Removing direction related tags for electric sp specs

Probably makes more sense to use negative and positive values for setpoints that control bidirectional electricity flow.

### Adding support for Current Demand Sensors

Need to add support for L1, L2, L3, and line avg of current demand.

TODO - might need to alter how we do power demand today:
 * Probably do not want stand alone `power` and `demand` tags
 * Should we introduce the tags `powerDemand` and `currentDemand`?

 ## Other

Not sure how to implement "Total Phase A Current" exactly.  Is that absolute sum of import & export measured values?  Need to think if that even makes sense.