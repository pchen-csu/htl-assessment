# htl-assessment
Files in this repository are supplementary to the journal article:
Chen, P. and Quinn, J.C. (2021). Microalgae to biofuels through hydrothermal liquefaction: Open-source techno-economic analysis and life cycle assessment. Applied Energy. Manuscript in print.

Aspen Plus instructions
-----------
Filename: HTL base model PChen.bkp

Functionality has only been validated for Aspen Plus V10.0. Minor changes may be required for older or newer versions of Aspen Plus.

The hydrothermal liquefaction (HTL) process model configuration is based on Knorr et al. (2013) ("Case B") and Jones et al. (2014).

Instructions in this section are largely focused on user inputs. Consult the authors if help is required for more advanced changes to the process model.

Properties tab:

- In Components, HTL- and upgrading-formed compounds are selected from Jones et al. (2014). Please refer to Jones et al. (2014) or the supplementary information from this work for more information about the compounds used in the model.
- In Selected Methods, relevant property methods include PSRK, SRK, PENG-ROB, and NRTL. If new components are added, Aspen Plus may prompt for additional inputs in the Parameters folder based on the selected property method. Refer to an Aspen Plus guide for more information.

Main flowsheet (Simulation tab):

- Follow the pathway Flowsheeting Options --> Calculator
  - The FEED calculator allows user input for algal feed properties, including cultivation productivity (or mass flow rate), molecular composition, and ultimate analysis. It is highly recommended that new inputs are validated against real feedstock data.
  - The HTL-HFLO calculator returns total enthalpy flow rates around the HTL heat exchanger for energy balance.
  - The HTL-RECY calculator returns the mass balance of recycled nutrients in the aqueous phase. Ultimate analysis inputs from the FEED calculator are called.
  
A100 hierarchy:

- This block covers the main HTL reactor and immediate auxiliary equipment ('HTL'), hydrotreating ('HT'), and hydrocracking ('HC').
  - A100.HTL requires the most user input.
    - It is recommended that the simulation stays in the PSRK method. However, if results for different property methods are desired, changes should only be made in this block. This can be accessed through the folder pathway Blocks --> A100 --> Blocks --> HTL --> Methods --> Specifications and changed in the 'Base method' drop-down menu. If NRTL is selected, the Henry components set 'HC' should be selected.
    - The HTL temperature must be set manually in process blocks E-304 and R-301. A temperature in the range of 260 - 350 °C is acceptable. Pressures do not need to be changed, as that is taken care of separately.
    - Optionally, the hot (aqueous phase) outlet temperature can be specified in block E-301. Aspen Plus may return a temperature crossover warning if the set temperature is not plausible.
    - The HTLHRXN calculator manually overrides the Aspen Plus-calculated heat of reaction in the 'ENDO-EXO' FSPLIT block. The user input can be 0 if heat of reaction is considered negligible, > 0 if known to be endothermic, and < 0 if known to be exothermic.
    - The PRESSURE calculator uses the temperature-dependent Antoine equation to roughly ensure that streams maintain a vapor fraction of 0. This calculator can be disabled and known pressure inputs can be specified in individual process blocks.
    - The YIELDS calulator takes user inputs for the known relative yields (must be mass fraction, not %) of biocrude, aqueous, solid, and gas phases and splits each phase into their corresponding components defined by the user in the Properties tab. Phase yields must add up to 1. Refer to the Excel file Aspen yields.xls to vary phase yields. Any component changes in the Properties tab must be accounted for in this calculator as well. Note that in reactor block R-301, water (H2O), salts (NH4SO4), and ash are treated as inert components.
  - A100.HT and A100.HC require minimal user input.
    - Both blocks have hydrogen duty calculators, 'HT-H2' and 'HC-H2', respectively. Hydrogen duty is typically scaled with respect to the input stream mass flow rate by a constant factor.
    - The upgrading process model is directly scaled from Jones et al. (2014). These process blocks may be changed to accommodate more robust data if it becomes available.

A200 hierarchy:

- This block covers the main heating utility component ('NGHEAT'), the low-pressure boiler / heat recovery system ('LPBOILER'), and the attached low-pressure steam generation loop ('STEAMGEN'). The latter two blocks are turned off by default.
  - A200.NGHEAT calculates the required supplemental natural gas duty given the required heat duties from all of A100 (QNG-REQ heat stream).
      - The design spec block 'NGMU' searches for the value of stream NGSUPP such that the energy balance from block FURNACE is resolved.
      - Volatile gases produced from A100 are routed to A200 and separated by an ammonia scrubber modeled as the simple separator block NH3SCRUB. The gas recycle is used for combined heat and power and reduces the supplemental natural gas duty required for HTL, but if excess volatile gases are produced, the supplemental natural gas duty will be calculated as 0.
  - A200.LPBOILER and A200.STEAMGEN are included for cases where heat recovery for low-pressure steam is considered. However, in this work, the power generated is negligible and these two blocks are switched off by default.

A300 hierarchy:

- This block covers remaining utilities - air cooling ('AIRCOOL') and cooling water ('CWTOWER').
  - A300.AIRCOOL uses the AIRFLOW design spec to calculate the air flow and power requirement for cooling fan blocks in A100.HT and A100.HC.
  - A300.CWTOWER calculates the cooling water flows and associated pumping requirements for water cooling in A100. Cooling tower fan circulation power is assumed negligible.
    - There is a CTWRCHEM calculator block commonly found in other National Renewable Energy Laboratory (NREL) process models. For more information, see the files in https://www.nrel.gov/extranet/biorefinery/aspen-models/


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


Excel instructions
-----------

Filename: HTL TEA LCA PChen.xlsm

'Setup' tab:

- Please note the disclaimer and additional information in this tab.
- Click the 'Load Aspen V10.0 file' to extract simulation results from the Aspen Plus file. Because of the directory locations in the macro code, the Aspen Plus file *must* be in V10.0. If using a different version of Aspen Plus, the directory locations must be changed in the 'Setup' tab VBA code.
- Aspen Plus results will be reported in the given units in cells G14:H28.
- Throughout the worksheet, orange cells can be changed directly by the user.

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

'CAPEX' tab:

- Capital equipment and scaling variables were derived from Knorr et al. (2013) and Jones et al. (2014).
- Additional equipment and capital cost data can be added in new rows above the grayed cells ("INSERT additional [area] equipment above").
- Boolean operators 0 or 1 in column A are used as a switch to exclude or include existing rows in calculations.

'OPEX' tab:

- Unit operating costs were derived from the sources listed in column K.
- Additional operating cost data can be added in new rows above the grayed cells ("INSERT additional [operating cost] above").
- Boolean operators 0 or 1 in column A are used as a switch to exclude or include existing rows in calculations.

'DCFROR' tab:

- The 'Solve feedstock price for MFSP target' button calculates the maximum algae feedstock price such that the target minimum fuel selling price (MFSP) entered in cell D26 can be achieved. The result is returned in cell D29.
- The 'Solve MFSP for given feedstock price' button calculates the HTL MFSP such that the net present value of the plant is $0 over a standard 30-year lifetime. The feedstock price in cell D29 must be specified; a value of 0 returns the MFSP contribution of just the HTL process. The result is returned in cells B35 ($/gallon gasoline equivalent) and B36 ($/liter gasoline equivalent).
- Ensure that the parameters in A3:C37 are valid. The cost year in cell B34 must be found within all tables in the 'CostIndex' tab.

'LifeCycleInventory' tab:

- Tool for Reduction and Assessment of Chemicals and Other Environmental Impacts (TRACI) v2.1 life cycle inventory data for all material and energy flows are listed in the table in D1:Q15.
  - By default, inventory data are geographically resolved around the southern United States wherever data is available. Inventory data from different geographical regions can be used in this table.
  - If new rows of inventory data are added, ensure that all cell dependents are traced correctly.
- The table in A18:O46 is end use (combustion) data from GREET 2019 and should not be changed.

'LifeCycleAssessment' tab:

- Most inputs in this tab are copied from the 'OPEX' tab. See 'OPEX' tab instructions for more information.
- The degree of credit accounting (0 - 100%) can be entered in place of Boolean operators in cells A38:A40.
- Total impacts are tabulated in I87:S87 and on a MJ fuel functional unit basis. Graphical representation of the life cycle impact breakdowns can be found below this row.

'Summary' tab:

- Miscellaneous TEA and LCA breakdowns can be found in this tab.
