ELAM in ACI

In ACI, we have a tool that we can use to troubleshoot forwarding issues between endpoints; it's called Embedded Logic Analyzer Module (ELAM). ELAM is a tool (not unique to ACI) that allows a user to set conditions in hardware and capture the first packet or frame that matches the set conditions. In ACI, a successful capture will cause the ELAM status to show as 'Triggered'. Once triggered, the ELAM is disabled and a dump can be collected to analyze the vast number of forwarding decisions that the switch ASIC is making with that packet/frame. ELAM is implemented at the ASIC level and will not impact CPU or other resources on the switch.

Before using the tool, it is important to understand the structure of the command syntax. Example on ACI leaf CLI:

Prepare the ELAM

• vsh_lc [this command enters the line card shell where ELAMs are run]

• debug platform internal <asic> elam asic 0 [refer to the ASICs table]*

*To understand which ASIC to use in the above command, run the 'show module' command in ishell first. If the switch model ends in 'EX', then use 'tah' in the above command. If the switch model ends in 'FX', then use 'roc' in the above command. Please note that this is only for Gen 2 switches; Gen 1 ELAMs are not covered here.

Set Conditions to Trigger

• trigger reset [ensures no existing triggers are running]

• trigger init in-select <number> out-select <number> [determines what information about a packet is displayed and which conditions can be set]*

*For non-VXLAN encapsulated traffic, use 'in-select 6' and 'out-select 0' (or 'out-select 1'). For VXLAN encapsulated traffic, use 'in-select 14' and 'out-select 0' (or 'out-select 1').

• set outer <traffic-type> <conditions> [sets conditions]*
OR
• set inner <traffic-type> <conditions> [sets conditions]*

*For non-VXLAN encapsulated traffic, use 'outer'. For VXLAN encapsulated traffic, use 'inner'.

• start [starts the trigger]

• status [checks if a packet is captured]*

*If a packet matching the defined conditions is detected on the ASIC, the output of 'status' will show 'Triggered'. If no packet matching the defined conditions is detected on the ASIC, the output of 'status' will show 'Armed'.

Generate the Dump

• ereport [display detailed information regarding the forwarding decision for the packet]*

*'ereport' can be used to display the result of the ELAM in an easy to understand format. Note that the ELAM report is also saved in the '/var/log/dme/log/' and '/tmp/logs' directories on the switch. There will be two files for the ELAM under each directory:

• elam_.txt 
• pretty_elam_.txt
