# RTL-synthesis-VSD-workshop
This is a RTL to FPGA workflow using _iverilog_, _GTKwave_ and _Yosys synthesis_ Tool. 
This is a 10 days Project by VSDIAT.


## Day 1 :

### Lab: Creating Workspace and simulate a 2-to-1 Multiplexer.

#### Step 1: Clone the Workshop Repository
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
#### Step 2: Install iverilog and GTKwave Tools
![Alt Text](workshop_snaps/1.png)

#### Step 3: Simulate the Design

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
