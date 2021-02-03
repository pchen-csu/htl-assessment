# htl-assessment
Files in this repository are supplementary to the journal article:
Chen, P. and Quinn, J.C. (2021). Microalgae to biofuels through hydrothermal liquefaction: Open-source techno-economic analysis and life cycle assessment. Applied Energy. Manuscript in print.

Aspen Plus instructions
-----------
Filename: HTL base model PChen.bkp

Functionality has only been validated for Aspen Plus V10.0. Minor changes may be required for older or newer versions of the Aspen Plus software.

The HTL configuration is based on process models from Knorr et al. (2013) ("Case B") and Jones et al. (2014).

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
'Setup' tab:

- Please note the disclaimer and additional information in this tab.
- Click the 'Load Aspen V10.0 file' to extract simulation results from the Aspen Plus file. Because of the directory locations in the macro code, the Aspen Plus file *must* be in V10.0. If using a different version of Aspen Plus, the directory locations must be changed in the 'Setup' tab VBA code.
- Aspen Plus results will be reported in the given units in cells G14:H28.

'StreamData' tab:

- Click the 'Clear data' button to remove the old data in columns C - E.
- Click the 'Populate data' button to fill in data from the file loaded in the 'Setup' tab.
- Columns C - E will return the variables requested in column B. Stream variables can be added in the stream ID format given in A4:A16.
  - Do not add user-defined variables from Aspen Plus (e.g. variables stored from Calculator blocks). These are accounted for automatically through the MASSFLOW sensitivity block.
- To call on a stream variable anywhere in the worksheet, use the VLOOKUP function with arguments

= VLOOKUP([Stream ID], STREAMDATA, 2, false)
  
'HTLEquipSize' tab:

- This tab calculates the HTL heat exchanger areas that are used as scaling variables for capital cost calculations.
- Results from the HTL-HFLO Calculator block in Aspen Plus are required.

'CostIndex' tab:

- Lists cost index data for equipment (cols. A - C), chemicals (cols. E - G), labor (cols. I - K), and electricity (cols. M - O).
- Any index years added or deleted must be accounted for in the 'StreamData' tab in cells I4:L7.
