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

### Removing direction related tags for electric sp specs

Probably makes more sense to use negative and positive values for setpoints that control bidirectional electricity flow.

### Adding support for Current Demand Sensors

Need to add support for L1, L2, L3, and line avg of current demand.

TODO - might need to alter how we do power demand today:
 * Probably do not want stand alone `power` and `demand` tags
 * Should we introduce the tags `powerDemand` and `currentDemand`?

### Specifying the type of magnitude for voltage and current

Magnitude types:
 * Peak
 * Peak-to-Peak
 * Average
 * RMS

Factors analyzing more than one signal:
 * THFF: measured in % units
 * THD
 * Odd THD
 * Even THD

Factors analyzing a single signal:
 * Crest factor: ratio of its peak (crest) value divided by its RMS value (% units)
 * Form factor: ratio of RMS value to its average value (% units)
 * K factor: measures heating effect caused by current harmonics (unitless).  A linear load has a K factor value of 1.  A K factor greater than one indicates the load is non-linear.  The higher the K factor the higher heating effect caused by harmonics in the system.

Notes:
 * We need a way to express a complete signal or its decomposed sinusoidal waveforms

Source:
 * https://www.allaboutcircuits.com/textbook/alternating-current/chpt-1/measurements-ac-magnitude/

## Other

 * Need clarification on bacnet addresses which are not specific to the input wiring for Acuvim2ElecMeter1LNOr1LLOr3LNWiring & Acuvim2ElecMeter2LLOr3LLWiring