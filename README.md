# htl-assessment
Files in this repository are supplementary to the journal article:
Chen, P. and Quinn, J.C. (2021). Microalgae to biofuels through hydrothermal liquefaction: Open-source techno-economic analysis and life cycle assessment. Applied Energy. Manuscript in print.

Aspen Plus instructions
-----------
Filename: HTL base model PChen.bkp

Functionality has only been validated for Aspen Plus V10.0. Minor changes may be required for older or newer versions of the Aspen Plus software.

Properties tab:
- In Components, HTL- and upgrading-formed compounds are selected from Jones et al. (2014). Please refer to Jones et al. (2014) or the supplementary information from this work for more information about the compounds used in the model.
- In Selected Methods, relevant property methods include PSRK, SRK, PENG-ROB, and NRTL. If new components are added, Aspen Plus may prompt for additional inputs in the Parameters folder. Refer to an Aspen Plus guide for more information.

Main flowsheet (Simulation tab):
- Follow the pathway Flowsheeting Options --> Calculator
  - The FEED calculator allows user input for algal feed properties, including cultivation productivity (or mass flow rate), molecular composition, and ultimate analysis. It is highly recommended that new inputs are validated against real feedstock data.
  - The HTL-HFLO calculator returns total enthalpy flow rates around the HTL heat exchanger for energy balance.
  - The HTL-RECY calculator returns the mass balance of recycled nutrients in the aqueous phase. Ultimate analysis inputs from the FEED calculator are called.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


Excel instructions
-----------
'Setup' tab

Please note the disclaimer and additional information in this tab.
