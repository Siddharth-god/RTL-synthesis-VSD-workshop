# RTL-synthesis-VSD-workshop
This is a RTL to FPGA workflow using _iverilog_, _GTKwave_ and _Yosys synthesizer_ Tool. 
This is a 10 days Project by VSDIAT.


## Day 1 :

### Lab: Creating workspace for simulating the designs. 

#### Step 1: Clone the Workshop Repository
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
#### Step 2: Install iverilog and GTKwave Tools
![Alt Text](workshop_snaps/1.png)

#### Step 3: Simulate the 2-to-4 Multiplexer.

Compile the design and testbench:
```bash
iverilog good_mux.v tb_good_mux.v
```
Run the simulation :
```bash
./a.out
```
View the waveform : 
```bash
gtkwave tb_good_mux.vcd
```
![Alt Text](workshop_snaps/run_good_mux_commands.png)
![Alt Text](workshop_snaps/run_good_mux_opwaveform.png)

### Verilog Code Of the design.
##### Code of the design (good_mux.v) :

To install gvim use commands :
```bash
sudo apt update
sudp apt install vim.gtk3
```
Command to view the design code of 2-to-4 Mux : 
```bash
gvim tb_good_mux.v -o good_mux.v
```
![Alt Text](workshop_snaps/good_mux_design_code.png)

## Introduction to Yosys 

#### What is Yosys ?
```
Yosys is a tool which convert the verilog HDL code into the circuit(logic) design for better visualization.
```
How Synthesis Works ? 
![Alt Text](workshop_snaps/synthesis.png)

- Some Points to be aware of : 

```

What is Library ? (.lib)
--> A library is a collection of pre-designed and characterized logic cells (like AND, OR,flip-flops) with information about their timing, power, and area, which the synthesizer uses to map RTL code into an optimized gate-level circuit.
```
```
Why do we need slow cells?
--> Slow cells are needed to intentionally delay signals so that data from one flip-flop (FFA) doesn’t reach the next flip-flop (FFB) too early, preventing data overlap or loss.(We need slow cells to meet our delay/hold.)
```
```
Why do we need small and fast cells?
--> Small and fast cells are used where quick signal propagation is required to meet timing constraints and improve the overall speed of the circuit.(We need fast cells to meet our setup)
```
What is setup time ? 
```
Setup time means we have to give the supply before the clock changes to stabilize the input and also the circuit.
what is Tclk : Tclk is the time taken by one clk to reach to another clk. It is 1clk cycle
```

#### HDL code to Synthesized Logic design.
![Alt Text](workshop_snaps/Hdl-logic.png)

## Synthesis (good_mux.v)

Commands to invoke the yosis and get the logical circuit

```bash
# Invoking the yosys
yosys

# Reading the library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Reading the Verilog file
read_verilog good_mux.v

#Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# To show the design created 
show

# Writing the netlist for given design
write_verilog good_mux_netlist.v

# To view the netlist using gvim
!gvim good_mux_netlist.v

# Simplyfying the netlist 
write_verilog -noattr good_mux_netlist.v

# Viewing the netlist again 
!gvim good_mux_netlist.v
```

Images of the synthesis process

![Alt Text](workshop_snaps/invoking_yosys.png)
![Alt Text](workshop_snaps/commands.png)
![Alt Text](workshop_snaps/commands2.png)
![Alt Text](workshop_snaps/ip_op_signals.png)
![Alt Text](workshop_snaps/logic_design.png)
![Alt Text](workshop_snaps/write_netlist.png)
![Alt Text](workshop_snaps/netlist.png)
![Alt Text](workshop_snaps/simplified_netlist.png)

# DAY 2













