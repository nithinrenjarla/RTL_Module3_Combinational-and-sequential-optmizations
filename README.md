
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

<img width="700"  alt="dff const1" src="https://github.com/user-attachments/assets/793836c1-e464-4b69-be9a-444711108c40" />


# 6. Verilog Code for D Flip-Flop Constant Propagation
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
<img width="700" alt="dfffff" src="https://github.com/user-attachments/assets/8e40e3af-156f-4827-b693-3926b49ab5df" />

# 7.Simulation – dff_const1
Description

Simulation waveform showing the behavior of dff_const1. The output changes according to the reset signal and clock edge.
# Code
```verilog
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
# Commands
```verilog
iverilog -o dff_const1.out dff_const1.v tb_dff_const1.v
vvp dff_const1.out
gtkwave tb_dff_const1.vcd
```
# 8. Simulation – dff_const2
Description
Simulation waveform of dff_const2 showing a constant output of 1 because both reset and normal-operation conditions assign q = 1'b1.
# code
```verilog
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
# commands
```bash
iverilog -o dff_const2.out dff_const2.v tb_dff_const2.v
vvp dff_const2.out
gtkwave tb_dff_const2.vcd
```
# 9. D Flip-Flop Netlist Before Optimization
Description The synthesized netlist before applying sequential optimization.
# commands
```verilog
yosys
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="700"  alt="dfff" src="https://github.com/user-attachments/assets/aa46ee2a-3c17-45d6-8222-d009df2d75e4" />

# 10. Sequential Logic Optimization Result
Description The optimized circuit after sequential constant propagation. Redundant logic is removed by the synthesis tool.

# Commands
```verilog
yosys
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="700" alt="dff_const2" src="https://github.com/user-attachments/assets/7f1cd827-5c10-46a2-a867-db9b59d2f6d9" />

# 11. D Flip-Flop Constraint Simulation
Information This experiment demonstrates the simulation of a D Flip-Flop with constant propagation. The waveform verifies the behavior of the flip-flop during reset and clock transitions.

# Code
```verilog
module dff_const3(input clk, input reset, output reg q);

always @(posedge clk)
begin
    if(reset)
        q <= 1'b0;
    else
        q <= 1'b1;
end

endmodule
```
<img width="700" alt="dff const3 gtk" src="https://github.com/user-attachments/assets/b42d7133-7f18-48f0-acc8-138cb3ff98dc" />

# 12. Synthesized D Flip-Flop Circuit
Information The D Flip-Flop design is synthesized using Yosys. The generated circuit is mapped to SKY130 standard cells.

# Commands
```verilog
yosys

read_verilog dff_const3.v

synth -top dff_const3

show
```
<img width="700" alt="dff const3 show" src="https://github.com/user-attachments/assets/b5390247-712b-48dd-b17d-720b1247698a" />

# 13. Counter Optimization
Information This experiment demonstrates optimization by removing unused outputs from a counter circuit.

# Code
```verilog
module counter_opt(input clk, input reset, output q);

reg [2:0] count;

assign q = count[0];

always @(posedge clk, posedge reset)
begin
    if(reset)
        count <= 3'b000;
    else
        count <= count + 1;
end

endmodule
```
# commands
```verilog
yosys

read_verilog counter_opt.v

synth -top counter_opt


show
```
# 14. Counter Optimization Result
Information The synthesized counter retains only the required logic after optimization.

# Commands
```verilog
yosys

read_verilog counter_opt.v

synth -top counter_opt

show
```
<img width="700" alt="counter_opt" src="https://github.com/user-attachments/assets/28b88513-b7a6-4b31-89bc-20b811954914" />

# 15. Optimized Counter Circuit
Information The optimized gate-level implementation contains only the necessary flip-flops and logic.

# Commands
```verilog
write_verilog -noattr counter_opt_net.v

gvim counter_opt_net.v
```
<img width="700" alt="counter_opt" src="https://github.com/user-attachments/assets/39f0a0fd-1dd7-4dd8-bec4-94ba36f9d763" />

# 16. Optimized Counter Netlist
Information The generated netlist shows the optimized hardware after synthesis.

# Commands
```verilog
write_verilog -noattr counter_opt_net.v

gvim counter_opt_net.v
```
<img width="700"  alt="counter opt editted" src="https://github.com/user-attachments/assets/d4edb5b6-1db9-4f40-b9f8-85331fb58b76" />



<img width="7 00"  alt="counter editted" src="https://github.com/user-attachments/assets/98ddd1b3-a187-4154-977e-5e27b8e4dff1" />


# 17. Importance of Optimization
◇ Area
Removing redundant logic can reduce the number of standard cells required, which can reduce the overall chip area.
◇ Power
Fewer unnecessary switching elements can reduce dynamic power consumption.
◇ Timing
Simplified logic can reduce propagation delay and improve timing performance.
◇ Hardware Efficiency
Optimization allows the required functionality to be implemented with fewer hardware resources.
Therefore, optimization is an essential step in converting RTL into an efficient physical implementation.

# 18. CONCLUSION
Module 3 provided practical experience with combinational and sequential RTL optimization using Yosys. The experiments demonstrated how RTL descriptions are transformed into optimized hardware structures.
The exercises covered Boolean simplification, constant propagation, removal of unnecessary logic, sequential optimization, flip-flop mapping, circuit visualization, and synthesis statistics.
The counter and flip-flop examples helped demonstrate how optimization techniques can be applied to sequential designs while maintaining their intended functionality.
Overall, the module provided a practical understanding of how RTL optimization influences hardware area, power, timing, and implementation efficiency.

