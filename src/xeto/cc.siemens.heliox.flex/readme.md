## Introduction

This library defines Xeto device specs for the Heliox Flex 180kW power cabinet and 250A dispenser by Siemens.

[Heliox Flex 180kW Power Cabinet](https://www.heliox-energy.com/us-products/flex-180kw-rapid-charger)

[Heliox Flex 250A Dispenser](https://www.heliox-energy.com/us-products/flex-250-a-dispenser)

## Notes

 * In the model number for the dispenser XXX needs to be replaced with an option
 * Need to revisit the dimensions and possibly introduce a depth attr
 * Not sure if it makes sense to have Input in `ElecDcRatedInputMaxCurrentAttr`
 * Might want to later model the AC incoming feed to the dispenser for control power
 * It is assumed each dispenser has a dedicated ocpp websocket connection
 * TODO: capture semantics for dynamic config - 83 / 167 / 250A
 * Not sure what the maximum input power rating is for the dispenser
 * TODO: consider adding elec tag to EvseEquip
 * Might need UL and IEC spec variants
