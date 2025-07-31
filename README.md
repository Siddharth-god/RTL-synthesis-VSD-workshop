# RTL-synthesis-VSD-workshop
This is a RTL to FPGA workflow using _iverilog_, _GTKwave_ and _Yosys synthesizer_ Tool. 
This is a 10 days Project by VSDIAT.


## Day 1 : Introduction to Verilog RTL design and Synthesis

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

Commands to invoke the yosys and get the logical circuit

```bash
# Invoking the yosys
yosys

# Reading the library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Reading the Verilog file
read_verilog good_mux.v

# Using library to use the logic gates
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

Invoking the Yosys
![Alt Text](workshop_snaps/invoking_yosys.png)

Reading Library
![Alt Text](workshop_snaps/commands.png)

Reading verilog code
![Alt Text](workshop_snaps/commands2.png)

Input and Output signals of the good mux
![Alt Text](workshop_snaps/ip_op_signals.png)

Invoking the logic design
![Alt Text](workshop_snaps/logic_design.png)

Writing the Netlist
![Alt Text](workshop_snaps/write_netlist.png)

Netlist
![Alt Text](workshop_snaps/netlist.png)

Simplified Netlist
![Alt Text](workshop_snaps/simplified_netlist.png)

<h>

## Day 2 : Timimg libs, hierarchical vs flat synthesis and efficient flop coding style

## Timing Libraries 
### Some points to be aware of :
- tt : Typical process corner.
- 025C : Represents a temperature of 25°C, relevant for temperature-dependent performance.
- 1v80 : Indicates a core voltage of 1.8V.
This naming convention clarifies which process, voltage, and temperature conditions the library models.

### Opening and Exploring the .lib File
To open the sky130_fd_sc_hd__tt_025C_1v80.lib file :

1. __Locate the file:__
```bash
cd sky130RTLDesignAndSynthesisWorkshop/lib

# Use ls to copy the name
ls
```
2. __Open the file:__
```bash
gvim sky130_fd_sc_hd__tt_025C_1v80.lib
```
Library file 
![Alt Text](Day2_snaps/turnoff_syntax.png)

Use the command to turnoff the syntax
```bash
# Shown in the image above
(shift) : syn off
```
![Alt Text](Day2_snaps/library.png)


### Behavioural models with _only_ logic gates and without any power port :

1. a2111o cell model : 
```bash
# In the view window add this command not in the linux window.
:sp ../my_lib/verilog_model/sky130_fd_sc_hd__a2111o.behavioural.v

# How A2111O is described:
A = AND stage.
O = OR stage (final stage).
2111 = Input distribution to the AND gates.
Interpretation of A2111O:

# There are 4 AND gates in the first stage:
1st AND gate = 2 inputs.
2nd AND gate = 1 input.
3rd AND gate = 1 input.
4th AND gate = 1 input.

The outputs of these AND gates are then fed into a single OR gate.
```
![Alt Text](Day2_snaps/behaviouralmodel.png)
![Alt Text](Day2_snaps/a2111o.png)

2. AND gate model 
```bash
# In the view window add this command not in the linux window.
:sp ../my_lib/verilog_model/sky130_fd_sc_hd__and2.behavioural.v
```
- AND gate comparision (and2_0 vs and2_2)
![Alt Text](Day2_snaps/and2.png)

- This image shows : How the width of the gates can change the power usage and time.
![Alt Text](Day2_snaps/and_comparision.png)


## Multiple Modules Synthesis

Commands to be followed : 

```bash
# Open the verilog_file : sky130RTLDesignAndSynthesisWorkshop/verilog_files
yosys

# Read the library
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read the verilog file
read_verilog multiple_modules.v

# Invoke the synthesis command
synth -top multiple_modules

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# See the logic design of multiple modules
show multiple_modules

# Write the verilog netlist
write_verilog -noattr multiple_modules_hier.v

# View the netlist using gvim
!gvim multiple_modules_hier.v

# Writ out a flat netlist
flatten

# Write flat netlist in verilog code
write_verilog multiple_modules_flat.v

# View the flat netlist
!gvim multiple_modules_flat.v

```

To split the window in gvim window use command

```bash
# Here the file_name can be anything you want to see.
:vsp multiple_module_hier.v 
```
- Initial commands
![Alt Text](Day2_snaps/synthesis_of_multiple_modules.png)

- Multiple Modules with it's Sub Modules
![Alt Text](Day2_snaps/multiple_modules.png)

- Mapping RTL (Register Transfer Level) design into a standard cell library(.lib)
![Alt Text](Day2_snaps/synthesis2.png)

- Hierarchical Design of Multiple_modules
![Alt Text](Day2_snaps/hierarchical_design3.png)

- Netlist of Multiple Modules
![Alt Text](Day2_snaps/netlist_mm4.png)

- Flatten Netlist
![Alt Text](Day2_snaps/flatten_netlist_mm5.png)

- Flatten Logic Design
![Alt Text](Day2_snaps/flatten_design5.png)

- Comparision between Hierarchical Netlist vs Flattened Netlist
![Alt Text](Day2_snaps/splitting_gvim_window_6.png)


## Sub Modules Synthesis

Commands to be followed : 

```bash
# Open the verilog_file : sky130RTLDesignAndSynthesisWorkshop/verilog_files
yosys

# Read the library
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read the verilog file
read_verilog multiple_modules.v

# Invoke the synthesis command
synth -top sub_module1

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# See the logic design of multiple modules
show 
```
- Sub_Module Logic Design 
![Alt Text](Day2_snaps/sub_module1.png)

## Flip-Flop Coding Styles 

Follow the commands to get the designs: 
```bash
# Open the directory
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

# Open d flip-flop files 
ls *dff*
```

### Asynchronous/Synchronous SET and RESET.

#### Asyncronous Reset _D flip-flop_

- Coding Style : 
```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
Commands to be followed : 
```bash
# Run the command 
cd iverilog dff_asyncres.v tb_dff_asyncres.v

# Get the vsd file
./a.out

# Get the waveform 
gtkwave tb_dff_asyncres.vcd
```
![Alt Text](Day2_snaps/asyncres_waveform.png)

- You can see in the second image the behaviour of the _Asynchronous Reset_. First on the clock edge our OUTPUT(Q) got HIGH but instantly we can see due to the _Reset_ our Q got _Low (0)_ again. Its because of the "Asynchronous" reset. 


![Alt Text](Day2_snaps/asyncres_behaviour.png)

### Asynchronous Set _D flip-flop_

- Coding Style : 
```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
Commands to be followed : 
```bash
# Run the command 
cd iverilog dff_async_set.v tb_dff_async_set.v

# Get the vsd file
./a.out

# Get the waveform 
gtkwave tb_dff_async_set.vcd
```
![Alt Text](Day2_snaps/async_set.png)

- You can see in the second image the behaviour of the _Asynchronous Set_. The Output(Q) goes HIGH when set is HIGH and after that clock gets HIGH and our OUTPUT remains HIGH till our set remains HIGH. Therefore it tells us that "Asynchronous" is _Idepentent of the clock_. 
- When set is HIGH, irresprctive of clock our _Output_ will remain in the same position it was at the start of the _Asynchronous Set_ -- And once our SET is gone our OUTPUT changes based on CLOCK.

![Alt Text](Day2_snaps/async_set_behaviour.png)

### Synchronous Reset

Coding Style : 
```verilog
module dff_syncres (input clk, input async_reset, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
Commands to be followed : 
```bash
# Run the command 
cd iverilog dff_syncres.v tb_dff_syncres.v

# Get the vsd file 
./a.out

# Get the waveform 
gtkwave tb_dff_syncres.vcd
```
![Alt Text](Day2_snaps/syncres_waveform.png)

- The RESET went HIGH but still our OUTPUT not getting "0" - Because the OUTPUT is waiting for clock as a stimulation and when clock goes HIGH we can see the OUTPUT goes LOW "0". This is how SYNCHRONOUS RESET behave. 

![Alt Text](Day2_snaps/syncres_behaviour.png)

- Once we enter into "always @" Block because _Posedge_ of the clock -- Then in the _Always Block_ Reset has given the _higher Priority_ than the Clock - and our OUTPUT will remain _Low_ Until reset is Present doesn't matter if clock is High or LOw. That you can see in the image below.

![Alt Text](Day2_snaps/syncres_behaviour2.png)


## Synthesis of Syncronous/Asyncronous SET and RESET

1. Asynchronous Reset : 

Commands to be followed : 
```bash

- Asynchronous reset: Overrides clock, setting q to 0 immediately.
- Edge-triggered: Captures d on rising clock edge if reset is low.

# Open the directory 
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

# Invoke Yosys
Yosys

# Reading library
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Reading Verilog file
read_verilog dff_asyncres.v

# Run Synthesis by choosing the design
synth -top dff_asyncres

# Give the dff command to only look for d flip-flops
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design using
show
```
![Alt Text](Day2_snaps/asyncres_synthesis.png)


2. Asynchronous Set : 

Commands to be followed : 
```bash

- Asynchronous set: Overrides clock, setting q to 1 immediately.

# Reading Verilog file
read_verilog dff_async_set.v

# Run Synthesis by choosing the design
synth -top dff_async_set

# Give the dff command to only look for d flip-flops
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design using
show
```
![Alt Text](Day2_snaps/async_set_synthesis.png)


3. Synchronous Reset : 

Commands to be followed : 
```bash

- Synchronous reset: Takes effect only on the clock edge.

# Reading Verilog file
read_verilog dff_syncres.v

# Run Synthesis by choosing the design
synth -top dff_syncres

# Give the dff command to only look for d flip-flops
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design using
show
```
![Alt Text](Day2_snaps/syncres_synthesis.png)

- We are getting the same output as we expected.

![Alt Text](Day2_snaps/syncres_op.png)

## Some special cases where _abc -liberty_ doesn't work :

1. mul2 : Run Commands in Yosys
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog mult_2.v

synth -top mul2

# When to not call liberty (when there is nothing to call - means when there are no cells in design from library)
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design
show

# Writing Netlist
write_netlist -noattr mul2_net.v

# View the Netlist
!gvim mul2_net.v
```
![Alt Text](Day2_snaps/calling_abcliberty.png)

- If you see here we have not used any cells or gates that's why our command didn't worked. 
- Our output has one more bit than which got appended with "zero" and input remain exact on both sides.
- Now if the relation between _a_ and _y_ is _2 x a = y_ then we just have to append one zero in the binary value of _a_ and that's our _output_. Therefore, this is what this special case shows to us. 

![Alt Text](Day2_snaps/special_case1.png)

![Alt Text](Day2_snaps/netlist_of_mul2.png)


2. mult8 : Run Commands in Yosys
```bash

read_verilog mult_8.v

synth -top mult8

# When to not call liberty (when there is nothing to call - means when there are no cells in design from library)
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design
show

# Writing Nelist
write_verilog -noattr mult_8.v

# View the Netlist
!gvim mult_8.v
```
- If you see in the white area there is written that _nuumber of cells_ 0. Which tell us that this is a special case where no cells are used and we can change the structure of design freely. 
![Alt Text](Day2_snaps/mult8.png)

![Alt Text](Day2_snaps/mult8_d.png)

![Alt Text](Day2_snaps/netlist_mult8.png)

<h>

## Day 3 : Combinational and Sequential Optimization

### Combinational Logic Optimization :

```bash
# Open the directory
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

# Open the optimization files directory 
ls *opt_check*
```

### Synthesis of opt modules

1. Opt_check 1 

Design Code :
```verilog
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```

Commands to Run Synthesis :

```bash
# Open Directory
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files 

# Invoke Yosys
yosys

# Read library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read verilog file 
read_verilog opt_check.v

# Get the file for synthesis 
synth -top opt_check

# Executing OPT_CLEAN pass (remove unused cells and wires).
opt_clean -purge

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the file 
show
```
- Logic design Optimized 

![Alt Text](Day3_snaps/opt_check1.png)

2. Opt_check 2 

Design Code :
```verilog
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```

Commands to Run Synthesis :
```bash
# Read verilog file 
read_verilog opt_check2.v

# Get the file for synthesis 
synth -top opt_check2

# Executing OPT_CLEAN pass (remove unused cells and wires).
opt_clean -purge

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the file 
show
```
- Logic design Optimized 

![Alt Text](Day3_snaps/opt_check2.png)

3. Opt_check 3

Design Code :
```verilog
module opt_check3 (input a , input b, input c , output y);
  assign y = a?(c?b:0):0;
endmodule
opt_check3.v (END)

```

Commands to Run Synthesis :

```bash
# Read verilog file 
read_verilog opt_check3.v

# Get the file for synthesis 
synth -top opt_check3

# Executing OPT_CLEAN pass (remove unused cells and wires).
opt_clean -purge

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the file 
show
```
- Logic design Optimized 

![Alt Text](Day3_snaps/opt_check3_3ipandgate.png)

- Explaination

![Alt Text](Day3_snaps/opt_check3_codeexplain.png)


4. Opt_check 4

Design Code :
```verilog
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```

Commands to Run Synthesis :

```bash
# Read verilog file 
read_verilog opt_check4.v

# Get the file for synthesis 
synth -top opt_check4

# Executing OPT_CLEAN pass (remove unused cells and wires).
opt_clean -purge

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the file 
show
```
- Logic design Optimized 

![Alt Text](Day3_snaps/opt_check4.png)

5. Multiple module opt 

Design Code :
```verilog
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 

endmodule
multiple_module_opt.v (END)
```

Commands to Run Synthesis :

```bash
# Read verilog file 
read_verilog multiple_module_opt.v

# Get the file for synthesis 
synth -top multiple_module_opt

# Flattening the design to convert the hierarchical structure into whole structure 
flatten

# --> Why we do flattening ? 
# In Yosys, flattening is used to transform a hierarchical design into a single-level netlist, which means all module instances are recursively inlined into one top-level module.

# Executing OPT_CLEAN pass (remove unused cells and wires).
opt_clean -purge

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the file 
show
```
- Logic design Optimized 

![Alt Text](Day3_snaps/multiple_module_opt_flattened.png)

6. Multiple module opt2

Design Code :
```bash

```

Commands to Run Synthesis :

```bash
# Read verilog file 
read_verilog multiple_module_opt2.v

# Get the file for synthesis 
synth -top multiple_module_opt2

# Flattening the design to convert the hierarchical structure into whole structure 
flatten

# --> Why we do flattening ? 
# In Yosys, flattening is used to transform a hierarchical design into a single-level netlist, which means all module instances are recursively inlined into one top-level module.

# Executing OPT_CLEAN pass (remove unused cells and wires).
opt_clean -purge

# Using library to use the logic gates
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the file 
show

# Dumping Netlist 
write_verilog -noattr multiple_module_opt2_netlist.v

# View the netlist
!gvim multiple_module_opt2_netlist.v
```
- Logic Design Optimized 

![Alt Text](Day3_snaps/multiple_module_opt2_flattened.png)

- Netlist (flattened) 

![Alt Text](Day3_snaps/mm_opt2_netlist_flattened.png)


### Sequential Optimization Techniques : 

Files to be used : 
```bash
# Open Directory 
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files 

# Open dff_const directory
ls *dff*const*
```

### D flip-flops Constant designs behaviour : 

1. Dff_const1

Design Code : 
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
dff_const1.v (END)
```
Commands to Run the Verilog Files : 
```bash
# Run in iverilog 
iverilog dff_const1.v tb_dff_const1.v

# Get the vcd file 
./a.out

# See the waverform
gtkwave tb_dff_const1.vcd
```
- Waveform of dff_const1

![Alt Text](Day3_snaps/dff_const1.png)

```
Explanation :
- When Reset is 0 the Q doesn't instantly change to 1 instead it waits for the positive clock edge as given in code and then it Q becomes 1.
Even though this is an asynchronous reset, the output q waits for the positive clock edge because asynchronous behavior only occurs when reset goes high. When reset becomes 0, it doesn't trigger the always @(posedge clk or posedge reset) block. So, q doesn't update immediately — it waits for the next rising edge of clk, where the else condition runs and sets q to 1.
```

#### Synthesis of dff_const1 

Commands to follow : 
```bash
# Read Library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read Verilog file 
read_verilog dff_const1.v

# Do syhthesis 
synth -top dff_const1

# Mapping the dff using dfflibmap
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design 
show
```

Our design has active high reset that's why synthesis given us inverter before the reset pin. You can see in the image below we have _Not_ gate attached to _Reset_.

- Design after synthesis dff_const 1

![Alt Text](Day3_snaps/dff_const1_synthesis.png)

- Number of flops in design 

![Alt Text](Day3_snaps/dff_const1_onedffcell.png)

<h>

2. Dff_const2

Design Code : 
```verilog

module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
  if(reset)
    q <= 1'b1;
  else
    q <= 1'b1;
end
endmodule
dff_const2.v (END)
```
Commands to Run the Verilog Files : 
```bash
# Run in iverilog 
iverilog dff_const1.v tb_dff_const2.v

# Get the vcd file 
./a.out

# See the waverform
gtkwave tb_dff_const2.vcd
```
- Waveform of dff_const2

![Alt Text](Day3_snaps/dff_const2.png)

#### Synthesis of dff_const2

Commands to follow : 
```bash
# Read Library -- If you are runnig directly after running the const1 then no need for reading library.
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read Verilog file 
read_verilog dff_const2.v

# Do syhthesis 
synth -top dff_const2

# Mapping the dff using dfflibmap
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design 
show
```

As our Q is always one, there was no need to use any flop in the design and that's why our design has no cells used from library.

- Design after synthesis

![Alt Text](Day3_snaps/dff_const2_synthesis.png)

- Number of flops in design 

![Alt Text](Day3_snaps/dff_const2_nocells_present.png)

<h>

3. dff_const 3

Design Code : 
```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
        if(reset)
        begin
                q <= 1'b1;
                q1 <= 1'b0;
        end
        else
        begin
                q1 <= 1'b1;
                q <= q1;
        end
end

endmodule
dff_const3.v (END)

```
Commands to Run the Verilog Files : 
```bash
# Run in iverilog 
iverilog dff_const1.v tb_dff_const3.v

# Get the vcd file 
./a.out

# See the waverform
gtkwave tb_dff_const3.vcd
```
- Waveform of dff_const3

![Alt Text](Day3_snaps/dff_const3.png)

#### Synthesis of dff_const3

Commands to follow : 
```bash
# Read Library -- If you are runnig directly after running the const1 then no need for reading library.
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read Verilog file 
read_verilog dff_const3.v

# Do syhthesis 
synth -top dff_const3

# Mapping the dff using dfflibmap
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the design 
show
```
- Synthesis Design dff_const 3

![Alt Text](Day3_snaps/dff_const3_synthesis.png)

- Design explanation 

![Alt Text](Day3_snaps/dff_const3_explain.png)


- Number of flops in the design 

![Alt Text](Day3_snaps/dff_const3_2flopcells.png)

<h>

4. dff_const 4

```
Every commands for next two designs (const4 and const5) and synthesis will be as same as we did for all the dff_constants. 
```
Design Code : 
```verilog
module dff_const4(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
        if(reset)
        begin
                q <= 1'b1;
                q1 <= 1'b1;
        end
        else
        begin
                q1 <= 1'b1;
                q <= q1;
        end
end

endmodule
dff_const4.v (END)
```
- Waveform for dff_count4

![Alt Text](Day3_snaps/dff_const4.png)

- Present flops in the circuit are 0. 

![Alt Text](Day3_snaps/dff_const4_noflopspresent.png)

- Synthesis Image for dff_const4

![Alt Text](Day3_snaps/dff_const4_synthesis.png)

<h>

4. dff_const 5

Design Code : 
```verilog
module dff_const5(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
        if(reset)  // Only one the posedge of the reset our reset will work.
        begin      // When reset becomes 0, our Q will change only when there is clk posedge.
                q <= 1'b0;
                q1 <= 1'b0;
        end
        else
        begin
                q1 <= 1'b1;
                q <= q1;
        end
end

endmodule
dff_const5.v (END)

```
- Waveform for dff_count5

![Alt Text](Day3_snaps/dff_const5.png)

- Present flops in the circuit are 0. 

![Alt Text](Day3_snaps/dff_const5_2flopspresent.png)

- Synthesis Image for dff_const5

![Alt Text](Day3_snaps/dff_const5_synthesis.png)


### Sequential Optimization for unused output

1. Counter_opt.v 

- Synthesis 

```bash 
# Open Directory 
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

# Invoke Yosys
yosys

# Read Library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read Verilog Code of design
read_verilog counter_opt.v

# Doing synthesis of design 
synth -top counter_opt

# Finding the D-flip-flop from library for design
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the synthesized design 
show
```

- Number of flops getting used 

![Alt Text](Day3_snaps/counter_opt_flopsused.png)

- Counter 1 optimized design using only one DFF. 

![Alt Text](Day3_snaps/counter_opt_synthesis_design.png)

```bash
# Open directory 
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

# Copy the code of counter_opt.v to new file named counter_opt2.v 
cp counter_opt.v counter_opt2.v

# open the new file and edit it
gvim counter_opt2.v

# Edit the file as shown in images below
```
- Previous code :

![Alt Text](Day3_snaps/cp_counter_opt2.png)

- Edited code (changes made = assign values changed / only one line changed)
```
To save edited content : Esc --> :w + enter = saved 
```
![Alt Text](Day3_snaps/edited_code_counter2.png)

- Run synthesis 

```bash
# Invoke yosys in verilog_files
yosys

# Read the library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read verilog code (edited)
read_verilog counter_opt2.v

# Do synthesis on the module -- we changed file name but our module remains same. 
synth -top counter_opt 

# Map the synthesized design using actual gates from the library described as (.lib)
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the disign 
show
```

- Number of flops getting used in circuit

![Alt Text](Day3_snaps/edited_counter2_noofflops.png)

- Unlike in previous design every flop is getting used here 

![Alt Text](Day3_snaps/edited_counter_synthesisdesign.png)


## Day 4 : GLS, Blocking and Non-BLocking, Synthesis Simulation Mismatch

### GLS Synth Sim Mismatch

1. Ternary operator Multiplexer.(ternary_operator_mux.v)

```bash
# Run the Simulation
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v

# Get the Dumped vcd file
./a.out

# Get the waveform 
gtkwave 
```
- Waveform 

![Alt Text](Day4_snaps/ternary_mux1_wf.png)

- Synthesis 

```bash
read_liberty -lib ../lib/

read_verilog ternary_operator_mux.v

synth -top ternary_operator_mux

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

write_verilog -noattr ternary_operator_mux.v

show
```

- Design Image after synthesis (without GLC)

![Alt Text](Day4_snaps/ternary_mux_synthesis.png)

- Performing GLC 

Commands to follow : 

```bash
# Run the simulation using GLC
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v

# Get the vcd file
./a.out

# get the waveform
gtkwave tb_ternary_operator_mux.vcd
```
- GLS Output Waveform

![Alt Text](Day4_snaps/glc_ternary_op_mux_wf.png)

2. Bad Multiplexer

Design Code : 

```verilog
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
        if(sel)
                y <= i1;
        else 
                y <= i0;
end
endmodule
```

- RTL Simulation 

Commands to follow : 

```bash 
iverilog bad_mux.v tb_bad_mux.v

./a.out

gtkwave tb_bad_mux.vcd
```
```
- Synthesis Simulation Mismatch :
If we see verilog code the always block has condition set as "sel" which is a problem as when select is low activities at "i0" are not 
sensed by the always block. Now when "select" goes high "i1" gets selected but as there is no activity on select and our "i1" is changing 
still our "output (y) is not channging and out multiplexer is acting like an "flop". And this is happening because we used "sel" as our 
condition so unless "sel" changes no activity will happen at the output. so it is giving the behaviour like an flop. 
```

![Alt Text](Day4_snaps/bad_mux_wf.png)

- Synthesis 

Commands to follow :
```bash 
read_liberty -lib ../lib/Sky30_fd_sc_hd

read_verilog bad_mux.v

synth -top bad_mux

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

write_verilog -noattr bad_mux_net.v

show
```
- GLS Commands :

```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v

./a.out

gtkwave tb_bad_mux.vsd
```

- No Mismatch after GLS


![Alt Text](Day4_snaps/bad_mux_gls_wf.png)

3. Blocking caveat (Blocking Assignment Example) : 

```verilog
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
        d = x & c;
        x = a | b;
end
endmodule
```
```bash
iverilog blocking_caveat.v tb_blocking_caveat.v

./a.out

gtkwave tb_blocking_caveat.vcd
```

- If you see the design code above we have used _Blocking Assignments_ which means first line gets executed first and after that second. So if we think about it the _x_ in first line has the unknown value and _x_ acts as flopped value (flop used in designs to store temp values)

- RTL Simulation :

```
We are getting completely opposite output. If you check in design if a & b = 1 , c = 1 it should give us d = 1 . But we're 
Getting 0, and on every clock you can see that strange behaviour and that is happening due to "Blocking Assignment" our simulator is looking at the previous value of "a" which is 1 and that's why we get such strange behaviour.
```
![Alt Text](Day4_snaps/explain_blocking_simulation.png)

![Alt Text](Day4_snaps/blocking_wf.png)


- Synthesis

```bash
read_verilog blocking_caveat.v

synth -top blocking_caveat

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

write_verilog -noattr blocking_caveat.v

show
```
- Logic design after Synthesis 

```
In design below you can see there's no latch connected so it's not going to look for the previous value.
We can avoid this condition if we used "Non_blocking Assignment" but we don't want to here because we are learning the
behaviour of "Blocking Assignment". 
```
![Alt Text](Day4_snaps/blocking_caveat_synthesis_design.png)

- GLS Commands

```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v

./a.out

gtkwave tb_blocking_caveat.vsd
```
- Waveform of blocking_caveat after performing GLS 

You can see in the image below, we are getting perfect output - compare it with image above. 

![Alt Text](Day4_snaps/blocking_gls_wf.png)


## Day 5 : Optimization in Synthesis

```bash
# Open the directory
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

# To see all the Incomplete files 
ls *incomp*

# To see all the design codes at once 
gvim *incomp* -o
```
- All files 

![Alt Text](Day5_snaps/incomp_all_files.png)

--- 

### Incomplete Statements

#### If's

1. __Incomplete if__

Design code : 
```verilog 
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
        if(i0)
                y <= i1;
end
endmodule
```

![Alt Text](Day5_snaps/incomp_if_asdlatch.png)

- Running Simulation 

```bash
iverilog incomp_if.v tb_incomp_if.v 

./a.out

gtkwave tb_incomp_if.vcd
```
- Waveform incomp if

``` Our code says if i0 is high our y becomes i1, and when i0 is low y = i1 but in waveform our y behaves like i0 when i0 is low and thats because,
When our i0 gets low our y gets latches on to it's last y output and continues it until i0 becomes high again and acts like D-latch and we get this waveform.
```
![Alt Text](Day5_snaps/incomp_if_simulation_wf.png)

- Running Synthesis

```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog incomp_if.v

synth -top incomp_if

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show
```

- If you see in the image below we are getting the latch instead of Mux. That shows us our waveform behaviour was Latch.

![Alt Text](Day5_snaps/D_latch_insteadofmux.png)

- We get inferred latch after running synthesis

![Alt Text](Day5_snaps/synthesis_inferred_latch.png)

2. __Incomplete if 2__

```
Commands will be same - only file name will change.
```

Verilog Design Code : 

```verilog
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
        if(i0)  // If i0 is high : y = i1 & when i0 is low : next condition
                y <= i1;
        else if (i2)  // If i2 is high : y = i3 & when i2 is low : next <con> But as we don't have it latches to previous output. 
                y <= i3;
end
endmodule
```
- Code Explaination for incomplete if 2

![Alt Text](Day5_snaps/inc_if2_code_explain.png)

- Running Simulation

- Waveform Explaination : 
```
When i0 = 1, Y = i1. When i0 = 0, Next conditon :
When i2 = 1, Y = i3. When i2 = 0, Y latches to it's previous output.
You can see the waveform below : Both conditions are there.
```
- Condition 1 : i0

![Alt Text](Day5_snaps/incomp_if2_wf.png)

- Condition 2 : i2

![Alt Text](Day5_snaps/incomp_if2_sec_cond.png)

- Running Synthesis

```
"To understand the design" remember this that when input goes to D in D-Latch means it's not latching 
And when it goes to Enable(E) pin means it latches. You might get confused by seeing D-latch present
in the end and taking inputs from both the Multiplexers. 
```
![Alt Text](Day5_snaps/getting_latch_if2.png)

![Alt Text](Day5_snaps/design_after-synthesis.png)

---

#### Cases 

1. __Incomplete case__

```verilog
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
        case(sel)
                2'b00 : y = i0;
                2'b01 : y = i1;
        endcase
end
endmodule
```
- Explanation 

![Alt Text](Day5_snaps/incomp_case_explain.png)

- Running simulation

![Alt Text](Day5_snaps/incomp_case_wf.png)

- Running Synthesis

![Alt Text](Day5_snaps/design_after_synthesis.png)


2. __Comp case__ 

```verilog
module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
        case(sel)
                2'b00 : y = i0;
                2'b01 : y = i1;
                default : y = i2; // We have default present here means if no condition is given then this is default value for y : No latch will be present in this design. 
        endcase
end
endmodule
```
Code Explained : 

![Alt Text](Day5_snaps/comp_case.png)

- Running Simulation 

```
We are getting perfect output using default. The 10 and 11 select lines are giving i2 as output. 
```
![Alt Text](Day5_snaps/comp_case_wf.png)

- Running Synthesis

![Alt Text](Day5_snaps/no_latch_comp_case.png)

Logic design 

![Alt Text](Day5_snaps/design_compcase_synthesis.png)


3. __Partial Case__ 

Verilog Code : 

```verilog 
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
        case(sel)
                2'b00 : begin
                        y = i0;
                        x = i2;
                        end
                2'b01 : y = i1;  // x value is not given here. Therefore this value will create latch.
                default : begin
                           x = i1;
                           y = i2;
                          end
        endcase
end
endmodule
```

```
🔹 What is a Latch in RTL?
A latch is a level-sensitive storage element.

Unlike flip-flops, which are edge-triggered, latches are transparent (i.e., they pass input to output) when the enable signal is active.

🔹 What does Latch Enabled mean?
It means:

The latch is currently transparent (i.e., passing the input to output) because the enable signal is active.

When enable = 1, the latch is open — input flows through to output.

When enable = 0, the latch holds (i.e., it keeps its last value).

🧠 So is it like:
“When there is enable, there is no latch (i.e., data passes through), and when enable is off, it behaves like memory”?

✅ Yes — exactly!

That's what we mean when we say "latch is enabled" — it means it's not latching (yet), it's letting data through.
```

- Running Synthesis 

Code Explained : 

![Alt Text](Day5_snaps/partial_case.png)

Cell Information :

![Alt Text](Day5_snaps/partial_case_latchpresent.png)

Design after Synthesis : 

![Alt Text](Day5_snaps/design_partialcase_synthesis.png)


4. __Bad Case__ 

Verilog Code :

```verilog
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
        case(sel)
                2'b00: y = i0;
                2'b01: y = i1;
                2'b10: y = i2;
                2'b1?: y = i3;  // Here, ? --> can be either 1 or 0. which confuses simulator and creates overlapping.
                //2'b11: y = i3;
        endcase
end

endmodule
```

- Running Simulation 

```bash
iverilog bad_case.v tb_bad_case.v

./a.out

gtkwave tb_bad_case.vsd
```

```
If you see in the waveform below our simulator is getting confused due to (? - Unknown) value and instead of 1 it picks 0,
which causes overlapping and our output latches to it's previous value. Synthesiser on the other hand will pick 1 because
it takes revelant values so it sees if I have 10 then next value should be 11. 
Which causes Synthesis Simulation Mismatch. 
```

![Alt Text](Day5_snaps/bad_case_wf.png)

- Running Synthesis 

Commands to follow : 
```bash
# Read the library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Read verilog code (edited)
read_verilog counter_opt2.v

# Do synthesis on the module -- we changed file name but our module remains same. 
synth -top counter_opt 

# Map the synthesized design using actual gates from the library described as (.lib)
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# View the disign 
show

# Write New file to Run GLS 
write_verilog -noattr bad_case_net.v
```

```
We saw the output was latching while simulation, but in synthesis there is no latch.
```
![Alt Text](Day5_snaps/no_latch_present_insynthesis.png)

- Design after Synthesis

![Alt Text](Day5_snaps/design_bad_case_synthesis.png)

 write_verilog -noattr bad_case_net.v

- Running GLS 

```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_case_net.v tb_bad_case.v

./a.out

gtkwave tb_bad_case.vsd
```
![Alt Text](Day5_snaps/gls_perfected_wf.png)

--- 

### For and Generate : 

- _For_ loop is always used inside _always block_ and we use for to _evaluating the expressions_. 
- _Generate_ is used outside of _always block_ every time and we use this for _instantiating_ the hardware such as OR, AND, NAND gates.  

### Using For :

1. #### Mux : Generating Mux using Normal _for loop_ :

```verilog
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
        if(k == sel)
                y = i_int[k];
end
end
endmodule
```

- Running Simulation : 

Commands to follow : 

```bash 
iverilog mux_generate.v tb_mux_generate.v

./a.out

gtkwave tb_mux_generate.vcd
```
- Waveform for mux_generate.v 

![Alt Text](Day5_snaps/mux_wf.png)


- Running Synthesis : 

Commands to follow : 
```bash 
# Invoke yosys in verilog files 
yosys

read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog mux_generate.v 

synth -top mux_generate

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show
```
- Design after synthesis : 

![Alt Text](Day5_snaps/design_mux_genereate_synthesis.png)


2. #### D-Mux : Generating D-Mux using normal _for loop_

- Code comparision using _case_ and _for loop_ 

![Alt Text](Day5_snaps/dmux_using_case&for.png)


- Using _case_ & _for_ : The output will be the same for both, just method is different. 
```
When we use case the code is easier to understand but 
the problem is that when the design gets larger in size
let's say - 256 : 1 Mux then we would have to write 
270 lines of code. 
While using for that code becomes little complicated 
but once we write the code then that code even works for 
larger designs such as 256 : 1 Mux or Dmux. 
```

- Running Simulation 

- __For case__

Commands to follow : 

```bash
iverilog demux_case.v tb_demux_case.v

./a.out

gtkwave tb_demux_case.vcd
```
- Design Waveform 

![Alt Text](Day5_snaps/case_wf.png)

- __For for__

Commands to follow : 

```bash
iverilog demux_generate.v tb_demux_generate.v

./a.out

gtkwave tb_demux_generate.vcd
```
![Alt Text](Day5_snaps/demux_generate_wf.png)


- Running Synthesis 

- __For Case__ 

Commands to follow : 

```bash 
# Invoke yosys in verilog files 
yosys

read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog mux_generate.v 

synth -top mux_generate

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show
```

![Alt Text](Day5_snaps/synthesis_case.png)

- __For for__ 

Commands to follow : 

```bash 
# Invoke yosys in verilog files 
yosys

read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog mux_generate.v 

synth -top mux_generate

abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show
```

![Alt Text](Day5_snaps/synthesis_for.png)

--- 

### Using Generate : 

1. __Ripple carry adder (rca.v)__

Verilog code : 

```verilog 
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
        for (i = 1 ; i < 8; i=i+1) begin
                fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
        end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));


assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```

- Running Simulation 

```bash
iverilog fa.v rca.v tb_rca.v  # Because we're instantiationg full adder

./a.out

gtkwave tb_rca.vsd
```
- rca Waveform :

![Alt Text](Day5_snaps/rca.wf.png)

- Running Synthesis 


-------------------------------------------------x THE END x---------------------------------------------------------

------------------------------------------------x THANK YOU x--------------------------------------------------------

## Certificate : 
