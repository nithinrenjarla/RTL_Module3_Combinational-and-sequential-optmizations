<img width="958" height="930" alt="dff" src="https://github.com/user-attachments/assets/2b1d4c27-76f5-47f0-b607-3c875eda40d2" />
# RTL_Module3_Combinational-and-sequential-optmizations
#Module 3 -Combinational and sequential optmizations using verilog and Yosys

# 1. Introduction
Module 3 focuses on optimizing RTL designs using Yosys synthesis tools. The experiments demonstrate how Verilog RTL is converted into an optimized hardware representation and how unnecessary logic, constant values, unused signals, and sequential elements can be simplified.
The module covers both combinational optimization and sequential optimization, with practical examples using counters, multiplexers, flip-flops, and other RTL structures.

# 2. Objectives
The main objectives of this module are:
Understand RTL-to-gate-level synthesis.
Study combinational logic optimization.
Understand constant propagation and logic simplification.
Identify and remove unused logic.
Study optimization of sequential circuits.
Understand flip-flop mapping to SKY130 standard cells.
Observe the synthesized circuit using show.
Analyze synthesis statistics.

# 3. Combinational Logic Optimization
3.1 Boolean Logic Optimization
Yosys can simplify Boolean expressions and convert them into efficient hardware structures.
Typical optimization operations include:
opt
opt_clean
opt_expr
opt_merge
opt_dff
opt_muxtree
These operations help eliminate redundant logic and simplify the synthesized circuit.
# Why optimization is required?
Without optimization, the synthesized circuit may contain:
Unnecessary gates
Redundant logic
Unused signals
Larger hardware
Increased power consumption
 <img width="700" alt="yosys" src="https://github.com/user-attachments/assets/4521d893-7c06-4228-b7b1-1dc28cf67eb0" />

# 4. Constant Propagation
Constant propagation replaces signals whose values are known constants with those constant values.
# Example
If:
assign y = a & 1'b1; 

Yosys can simplify this to:

assign y = a;

Similarly,

assign y = a & 1'b0;

can be simplified to:

assign y = 1'b0;

This reduces unnecessary hardware.

# 5. Sequential Logic Optimization
Sequential optimization deals with circuits containing memory elements such as flip-flops and registers.
Yosys identifies the sequential behavior described in Verilog and maps it to suitable flip-flop cells.
For example:

always @(posedge clk, posedge reset)
begin
    if(reset)
        q <= 1'b0;
    else
        q <= 1'b1;
end

This represents a flip-flop with reset behavior.

<img width="700" alt="dff_const1.v" src="https://github.com/user-attachments/assets/c3790bd2-968e-4208-aebf-74de7217f872" />


6. Verilog Code for D Flip-Flop Constant Propagation
Description
This Verilog code demonstrates sequential constant propagation using two D Flip-Flop designs (dff_const1 and dff_const2).
The examples show how synthesis optimization depends on whether the output Q can always be reduced to a constant value. In dff_const1, Q is not always constant because reset can force it to 0. In dff_const2, Q is always 1, so the flip-flop becomes redundant during synthesis.
### Verilog Code – dff_const1.v
```verilog
// dff_const1.v
module dff_const1(input clk, input reset, output reg q);

always @(posedge clk, posedge reset)
begin
    if(reset)
        q <= 1'b0;
    else
        q <= 1'b1;
end

endmodule
```
### Verilog Code – dff_const2.v
```verilog
// dff_const2.v
module dff_const2(input clk, input reset, output reg q);

always @(posedge clk, posedge reset)
begin
    if(reset)
        q <= 1'b1;
    else
        q <= 1'b1;
end

endmodule
```
# Commands
Open the Verilog files using:
```verilog
vim dff_const1.v
vim dff_const2.v
```
<img width="700"  alt="dff" src="https://github.com/user-attachments/assets/150f07e8-ad53-4c91-ace7-80c8720487ab" />



▣ 7. RTL Visualization Using show
The Yosys show command generates a graphical representation of the synthesized design.
Example:
yosys> show
The resulting diagram helps us understand:
Inputs
Outputs
Logic cells
Flip-flops
Connections between cells
In your experiment, the generated diagram showed a SKY130 flip-flop cell connected to the reset and output signals.
📸 Screenshot 7 – Keep here:
The screenshot showing the Graphviz/Yosys circuit diagram with:
sky130_fd_sc_hd__...
This should go immediately below this section.
▣ 8. Synthesis Statistics
Yosys provides statistics after synthesis to show how the design has been converted into hardware.
Important information includes:
Parameter
Meaning
Number of wires
Total wires in the design
Number of ports
Input/output ports
Number of cells
Hardware cells used
$DFF / $DFF_PP
Flip-flop elements
Number of processes
Procedural blocks
Number of memories
Memory elements
For example, your terminal displayed:
=== dff_const3 ===

Number of wires:       4
Number of wire bits:   4
Number of public wires: 4
Number of ports:       3
Number of cells:       2
📸 Screenshot 8 – Keep here:
Your screenshot showing:
“Printing statistics”
and
“Found and reported 0 problems.”
▣ 9. Counter Optimization
A counter is a sequential circuit that changes its stored value on every clock edge.
Your counter_opt2.v contains:
reg [2:0] count;

assign q = count[0];

always @(posedge clk, posedge reset)
begin
    if(reset)
        count <= 3'b000;
    else
        count <= count + 1;
end
Here, the 3-bit register increments by one on every positive clock edge.
The output is taken from:
count[0]
Therefore, the output changes according to the least significant bit of the counter.
📸 Screenshot 9 – Keep here:
Your screenshot of counter_opt2.v code in GVim.
▣ 10. Optimization of Counter Logic
The counter demonstrates how sequential logic can be optimized while preserving the required functionality.
The synthesis process can:
Remove unnecessary logic.
Simplify constant expressions.
Optimize register connections.
Reduce redundant hardware.
Map registers to available standard cells.
📸 Screenshot 10 – Keep here:
Terminal screenshot showing the Yosys optimization process for the counter.
▣ 11. Verification / Check Pass
After optimization, Yosys performs checks to identify obvious problems in the synthesized design.
The terminal output may show:
Executing CHECK pass
Checking module ...
Found and reported 0 problems.
This indicates that no obvious structural problems were detected during the check.
📸 Screenshot 11 – Keep here:
Use your screenshot where the terminal clearly shows:
“Found and reported 0 problems.”
▣ 12. Key Observations
KEY OBSERVATIONS
RTL descriptions can be converted into optimized hardware structures.
Boolean expressions can be simplified during synthesis.
Constant propagation removes unnecessary logic.
Unused wires and cells can be eliminated.
Sequential RTL is represented using flip-flop elements.
Generic flip-flops can be mapped to SKY130 standard cells.
The show command provides a graphical view of the synthesized design.
Yosys statistics help analyze the hardware generated from RTL.
Counter circuits contain both sequential and combinational behavior.
Optimization reduces unnecessary hardware while maintaining functionality.
▣ 13. Importance of Optimization
◇ Area
Removing redundant logic can reduce the number of standard cells required, which can reduce the overall chip area.
◇ Power
Fewer unnecessary switching elements can reduce dynamic power consumption.
◇ Timing
Simplified logic can reduce propagation delay and improve timing performance.
◇ Hardware Efficiency
Optimization allows the required functionality to be implemented with fewer hardware resources.
Therefore, optimization is an essential step in converting RTL into an efficient physical implementation.
▣ 14. Conclusion
CONCLUSION
Module 3 provided practical experience with combinational and sequential RTL optimization using Yosys. The experiments demonstrated how RTL descriptions are transformed into optimized hardware structures.
The exercises covered Boolean simplification, constant propagation, removal of unnecessary logic, sequential optimization, flip-flop mapping, circuit visualization, and synthesis statistics.
The counter and flip-flop examples helped demonstrate how optimization techniques can be applied to sequential designs while maintaining their intended functionality.
Overall, the module provided a practical understanding of how RTL optimization influences hardware area, power, timing, and implementation efficiency.

