 # **# VSD-HDP-Manhattan-Project**
> -  Inspired by J.R.Oppenheimer

 ## Tools installation 
   [1]: #tools-installation

##### <u>Yosys</u>

~~~
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys
$ sudo apt install make (If make is not installed please install it) 
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make 
$ sudo make install
~~~
&nbsp;
![](images/Screenshot%20from%202023-07-23%2014-05-07.png)


&nbsp;
&nbsp;
  
#### <u>iverilog</u>

```
sudo apt-get install iverilog
```

![](images/Screenshot%20from%202023-07-23%2014-14-12.png)

&nbsp;
&nbsp;


#### <u>gtkwave</u>

```
sudo apt update
sudo apt install gtkwave
```

![](images/Screenshot%20from%202023-07-23%2014-15-44.png)


#### <u>OpenSTA</u>
```plaintext
sudo apt-get install cmake clang gcctcl swig bison flex
git clone https://github.com/The-OpenROAD-Project/OpenSTA.git
cd OpenSTA
mkdir build
cd build
cmake ..
make
```

![](images/day8/stas.png)

#### <u>ngspice</u>

https://sourceforge.net/projects/ngspice/files/ use this link to get the latest version ngspice file in my case its version 41 then follow the commands to extraxt and install the tool.

```plaintext
tar -zxvf ngspice-41_64.tar.gz
cd ngspice-41
mkdir release
cd release
../configure  --with-x --with-readline=yes --disable-debug
make
sudo make install
```


![](images/day10/2.png)

#### <u>magic</u>
```plaintext
sudo apt-get install m4
sudo apt-get install tcsh
sudo apt-get install csh
sudo apt-get install libx11-dev
sudo apt-get install tcl-dev tk-dev
sudo apt-get install libcairo2-dev
sudo apt-get install mesa-common-dev libglu1-mesa-dev
sudo apt-get install libncurses-dev
```

![](images/magic.png)


&nbsp;
&nbsp;
&nbsp;


### Day 1
<details>
<summary>Short Summary</summary>

The Purpose of this is to know the basic idea about the different tools in flow and use the good_mux.v to verify the functionality in iverilog by giving the stimulus and code we get the .vcd file to view in the gtkwave and next id to generate the netlist using the yosys tool . The Design and the Liberty file are given to the tool to generate the netlist using the skywater 130 Standard cell Library.
</details>
<details>
<summary>Source Files:</summary>

The verilog Codes and Liberty files are available using this repo 
https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git


I have used good_mux.v which is simple mux to verify its functionality in iverilog and gtkwave.


<br>
</details>


<details>
<summary>Simulation :</summary>

These are following commands used to simulate view waveforms 

```
iverilog <name verilog: good_mux.v> <name testbench: tb_good_mux.v>
./a.out
gtkwave tb_good_mux.vcd
```
![](images/gtkwave.png)
<br>
</details>


<details>
<summary>Synthesis aka Yosys :</summary>
Follow the commands to synthesize the design to get the design view.

```
yosys> read_liberty -lib <path to lib file>
yosys> read_verilog <path to verilog file>
yosys> synth -top <top_module_name>
yosys> abc -liberty <path to lib file>
yosys> show
```
![](images/synthesis.png)

following Commands are used to generate the netlist
```
yosys> write_verilog <file_name_netlist.v>
yosys> write_verilog -noattr <file_name_netlist.v>
```
![](images/netlist.png)
<br>
</details>

&nbsp;

### Day 2
<details>

<summary>Short Summary</summary>

The main purpose is to learn about the .lib which is a library file consists of information about the PVT corners , leakage power, area , cell delay all other information are formatted in liberty format.  It consists are variety of versions for single cell to used in multiple scenarios each cell has it own pros and cons regarding delay , area , performance . 
Next is to synthesize the multiple_modules.v in different synthesis methods(Hierarachial vs Flat) and next is to synthesize in sub-module level , where bottom-up approach is used to optimize the design and the run time of the tool other thing is the Module Instantiation technique to synthesize once and instantaite multiple time in the designs.
</details>

<details>
<summary>Synthesis :</summary>
Follow the commands to synthesize the design to get the design view.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: multiple_modules.v>
yosys> synth -top <name: multiple_modules>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show <name: multiple_modules>
yosys> write_verilog -noattr <name: multiple_modules_hier.v>
```
![](images/hierarachialsynthesis.png)

The following is the netlist of the design 
![](images/mulnetlist.png)

To get the Flatten version of synthesis use the following commands:
```
yosys> flatten
yosys> write_verilog -noattr <name: multiple_modules_flat.v>
```

![](images/flatten.png)![](images/flatnetlist.png)

<br>
</details>


<details>
<summary>Synthesis:Sub-module Level</summary>
Follow the commands to synthesize the design to get the design view.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: multiple_modules.v>
yosys> synth -top <name: sub_module1>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show <name: sub_module1>
```

![](images/submodule1.png)![](images/submodule2.png)

<br>
</details>

<details>
<summary>Simulation: Dff_async_reset</summary>
Follow the commands to simulate and view the waveforms.

```
iverilog <name verilog: dff_asyncres.v> <name testbench: tb_dff_asyncres.v>
./a.out
gtkwave <name vcd file: tb_dff_asyncres.vcd>
```
![](images/asyncresreset.png)
<br>
</details>

<details>
<summary>Simulation: Dff_async_set</summary>
Follow the commands to simulate and view the waveforms.

```
iverilog <name verilog: dff_async_set.v> <name testbench: tb_dff_async_set.v>
./a.out
gtkwave <name vcd file: tb_dff_async_set.vcd>
```
![](images/async%20set.png)
<br>
</details>

<details>
<summary>Simulation: Dff_sync_reset</summary>
Follow the commands to simulate and view the waveforms.

```
iverilog <name verilog: dff_syncres.v> <name testbench: tb_dff_syncres.v>
./a.out
gtkwave <name vcd file: tb_dff_syncres.vcd>
```
![](images/syncres.png)
<br>
</details>

<details>
<summary>Synthesis: Dff_async_reset</summary>
Follow the commands to synthesize the design and view the design .

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_asyncres.v>
yosys> synth -top <name: dff_asyncres>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show <name: dff_asyncres>
```
![](images/asyncresreset1.png)
<br>
</details>

<details>
<summary>Synthesis: Dff_asyncset</summary>

Follow the commands to synthesize the design and view the design .
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_async_set.v>
yosys> synth -top <name: dff_async_set>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show <name: dff_async_set>
```
![](images/asyncset1.png)
<br>
</details>

<details>
<summary>Synthesis: Dff_sync_reset</summary>

Follow the commands to synthesize the design and view the design .
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_syncres.v>
yosys> synth -top <name: dff_syncres>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show <name: dff_syncres>
```
![](images/dffsync1.png)
<br>
</details>

<details>
<summary>Synthesis: Mult_2.v </summary>

Optimization of special circuits here it is the multipler .Follow the commands to synthesize the design and view the design .
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: mult_2.v>
yosys> synth -top <name: mul2>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show <name: mul2>
yosys> write_verilog -noattr <name: mul2_net.v>
```
&nbsp;
```
Here there no special cells are used to synthesize the design because it doesnt require and special hardware circuit because multiplying 3 bit number with two gives appending of  1 zeros in LSB . For example 2 in binary is 010 if multipled by 2 is 4 0100
```

![](images/mul2.png)
&nbsp;
![](images/mul2net.png)
<br>
</details>

<details>
<summary>Synthesis: Mult_8.v </summary>

Optimization of special circuits here it is the multipler .Follow the commands to synthesize the design and view the design .
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: mult_2.v>
yosys> synth -top <name: mul2>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show <name: mul2>
yosys> write_verilog -noattr <name: mul2_net.v>
```
&nbsp;
```
Here there no special cells are used to synthesize the design because it doesnt require and special hardware circuit because multiplying 3 bit number with nine gives appending of  same number in LSB . For example 2 in binary is 010 if multipled by 9 is 18 010010.
```

![](images/mul8.png)

&nbsp;

![](images/mult8netlist.png)

<br>
</details>

### Day 3


<details>

<summary>Short Summary</summary>

The main purpose is to learn about the different optimizations used in combinational and sequential circuits namely Constant Propagation Method , State  Reduction , Retiming ,Logic Cloning .

![](images/day3/Notes_230813_165258_1.jpg)

![](images/day3/Notes_230813_165258_2.jpg)

![](images/day3/Notes_230813_165258_3.jpg)
</details>

<details>
<summary>opt_check.v</summary>


![](images/day3/Notes_230813_165258_4.jpg)

Here the mux is optimized into and gate.
&nbsp;
use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file:opt_check.v>
yosys> synth -top <name: opt_check>
yosys> opt_clean -purge
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/1.png)

![](images/day3/1.1.png)

</details>

<details>
<summary>opt_check2.v</summary>




Here the mux is optimized into or gate.
&nbsp;
use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: opt_check2.v>
yosys> synth -top <name: opt_check2>
yosys> opt_clean -purge
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```


![](images/day3/2.png)

![](images/day3/2.1.png)


</details>

<details>
<summary>opt_check3.v</summary>



![](images/day3/Notes_230813_165258_5.jpg)


Here the mux is optimized into 3-input and gate.
&nbsp;
use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: opt_check3.v>
yosys> synth -top <name: opt_check3>
yosys> opt_clean -purge
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```


![](images/day3/3.png)

![](images/day3/3.1.png)


</details>

<details>
<summary>opt_check4.v</summary>

use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: opt_check4.v>
yosys> synth -top <name: opt_check4>
yosys> opt_clean -purge
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```


![](images/day3/4.png)

![](images/day3/4.1.png)
</details>

<details>
<summary>multiple_module_opt.v</summary>

use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: multiple_module_opt.v>
yosys> synth -top <name: multiple_module_opt>
yosys> flatten 
yosys> opt_clean -purge
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/multopt1.png)


</details>

<details>
<summary>multiple_module_opt2.v</summary>

use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: multiple_module_opt.v>
yosys> synth -top <name: multiple_module_opt>
yosys> flatten 
yosys> opt_clean -purge
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/multopt2.png)


</details>

<details>
<summary>dff_const1.v</summary>


![](images/day3/Notes_230813_165258_6.jpg)


use following commands to simulate the design 
```
iverilog <name verilog: dff_const1.v> <name testbench: tb_dff_const1.v>
./a.out
gtkwave tb_dff_const1_.vcd
```


![](images/day3/dff1v.png)

use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_const1.v>
yosys> synth -top <name: dff_const1>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/dffconst1.png)


</details>

<details>
<summary>dff_const2.v</summary>



![](images/day3/Notes_230813_165258_7.jpg)



use following commands to simulate the design 
```
iverilog <name verilog: dff_const2.v> <name testbench: tb_dff_const2.v
./a.out
gtkwave tb_dff_const2_.vcd
```



![](images/day3/dffconst2v.png)


use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_const2.v>
yosys> synth -top <name: dff_const2>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/dffconst2.png)


</details>

<details>
<summary>dff_const3.v</summary>




![](images/day3/Notes_230813_165258_8.jpg)




use following commands to simulate the design 
```
iverilog <name verilog: dff_const3.v> <name testbench: tb_dff_const3.v
./a.out
gtkwave tb_dff_const3_.vcd
```



![](images/day3/dffconst3v.png)


use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_const3.v>
yosys> synth -top <name: dff_const3>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/dffconst3.png)


</details>

<details>
<summary>dff_const4.v</summary>




![](images/day3/Notes_230813_165258_9.jpg)




use following commands to simulate the design 
```
iverilog <name verilog: dff_const4.v> <name testbench: tb_dff_const4.v
./a.out
gtkwave tb_dff_const4_.vcd
```



![](images/day3/dffconst4v.png)


use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_const4.v>
yosys> synth -top <name: dff_const4>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/dffconst4.png)


</details>

<details>
<summary>dff_const5.v</summary>




![](images/day3/Notes_230813_165258_10.jpg)




use following commands to simulate the design 
```
iverilog <name verilog: dff_const5.v> <name testbench: tb_dff_const5.v
./a.out
gtkwave tb_dff_const5_.vcd
```



![](images/day3/dffconst5v.png)


use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: dff_const5.v>
yosys> synth -top <name: dff_const5>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day3/dffconst5.png)


</details>

<details>
<summary>counter_opt.v</summary>


use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: counter_opt.v>
yosys> synth -top <name: counter_opt>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```


![](images/day3/counteropt.png)

</details>

<details>
<summary>counter_opt2.v</summary>


use following commands to synthesize the design 
```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: counter_opt2.v>
yosys> synth -top <name: counter_opt2>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```


![](images/day3/counteropt2.png)

</details>

### Day 4

<details>

<summary>Short Summary</summary>

The main purpose is to learn about the Gate Level Simulation known as Post-synthesis Simulation to verify the functionality and Logical correctness  of Circuit is same as the pre-synthesis model. 

![](images/day%204/Screenshot%202023-08-14%20112419.png)

![](images/day%204/WhatsApp%20Image%202023-08-14%20at%2012.54.00%20PM.jpeg)

![](images/day%204/WhatsApp%20Image%202023-08-14%20at%2012.54.00%20PM%20(1).jpeg)

![](images/day%204/WhatsApp%20Image%202023-08-14%20at%2012.54.00%20PM%20(2).jpeg)

</details>


<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of ternary_operator_mux.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: ternary_operator_mux.v> <name testbench: tb_ternary_operator_mux.v>
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

![](images/day%204/ternary.png)

The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: ternary_operator_mux.v>
yosys> synth -top <name: ternary_operator_mux>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> write_verilog -noattr <name of netlist: ternary_operator_mux_net.v>
yosys> show
```


![](images/day%204/ternary1.png)

![](images/day%204/ternary%20operatornetlist.png)

The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to sky130_fd_sc_hd__tt_025C_1v80.lib: ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib> <name netlist: ternary_operator_mux_net.v> <name testbench: tb_ternary_operator_mux.v>
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

![](images/day%204/glsternary.png)

</details>

<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of bad_mux.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: bad_mux.v> <name testbench: tb_bad_mux.v>
./a.out
gtkwave tb_bad_mux.vcd
```


![](images/day%204/badmux.png)


The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: bad_mux.v>
yosys> synth -top <name: bad_mux>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> write_verilog -noattr <name of netlist: bad_mux_net.v>
yosys> show
```


![](images/day%204/badmux1.png)

![](images/day%204/badmuxnetlist.png)


The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to sky130_fd_sc_hd__tt_025C_1v80.lib: ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib> <name netlist: ternary_operator_mux_net.v> <name testbench: tb_ternary_operator_mux.v>
./a.out
gtkwave tb_ternary_operator_mux.vcd
```


![](images/day%204/glsbadmux.png)


</details>


<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of blocking  
_caveat.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: blocking_caveat.v> <name testbench: tb_blocking_caveat.v>
./a.out
gtkwave tb_blocking_caveat.vcd
```


![](images/day%204/blockcleat.png)



The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: blocking_caveat.v>
yosys> synth -top <name: blocking_caveat>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> write_verilog -noattr <name of netlist: blocking_caveat_net.v>
yosys> show
```



![](images/day%204/blockcleat.png)

![](images/day%204/blockcnetlist.png)




The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to verilog model: ../mylib/verilog_model/sky130_fd_sc_hd.v> <name netlist: blocking_caveat_net.v> <name testbench: tb_blocking_caveat.v>
./a.out
gtkwave tb_blocking_caveat.vcd
```



![](images/day%204/glsblockigc.png)

</details>

### Day 5

<details>

<summary>Short Summary</summary>


The main purpose is to learn about the different constructs or looping  statements like if , case , for , generate are used in the design . To know about the caveats of it like improper coding style , missing blocks can leads to "inferred Latch " in the design.

![](images/day5/WhatsApp%20Image%202023-08-15%20at%206.13.06%20PM.jpeg)

![](images/day5/WhatsApp%20Image%202023-08-15%20at%206.13.06%20PM%20(1).jpeg)

![](images/day5/WhatsApp%20Image%202023-08-15%20at%206.13.06%20PM%20(2).jpeg)

![](images/day5/WhatsApp%20Image%202023-08-15%20at%206.13.06%20PM%20(3).jpeg)

</details>

<details>

<summary> simulation , Synthesis, of incomp_if.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: incomp_if.v> <name testbench: tb_incomp_if.v>
./a.out
gtkwave tb_incomp_if.vcd
```


![](images/day5/incompif.png)




Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: incomp_if.v>
yosys> synth -top <name: incomp_if>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```



![](images/day5/incompif1.png)


</details>
<details>

<summary> simulation , Synthesis, of incomp_if2.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: incomp_if2.v> <name testbench: tb_incomp_if2.v>
./a.out
gtkwave tb_incomp_if2.vcd
```



![](images/day5/incompif2.png)





Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: incomp_if2.v>
yosys> synth -top <name: incomp_if2>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```



![](images/day5/incompif2a.png)



</details>

<details>

<summary> simulation , Synthesis, of incomp_case.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: incomp_case.v> <name testbench: tb_incomp_case.v>
./a.out
gtkwave tb_incomp_case.vcd
```


![](images/day5/incompcase.png)



Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: incomp_case.v>
yosys> synth -top <name: incomp_case>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```
![](images/day5/incompcasea.png)

</details>

<details>

<summary> simulation , Synthesis, of comp_case.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: comp_case.v> <name testbench: tb_comp_case.v>
./a.out
gtkwave tb_comp_case.vcd
```



![](images/day5/compcase.png)




Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: comp_case.v>
yosys> synth -top <name: comp_case>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day5/compcasea.png)

</details>

<details>

<summary>  Synthesis, of partial_case_assign.v </summary>


Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: partial_case_assign.v>
yosys> synth -top <name: partial_case_assign>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day5/partialcase.png)


</details>

<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of bad_case.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: bad_case.v> <name testbench: tb_bad_case.v>
./a.out
gtkwave tb_bad_case.vcd
```



![](images/day5/badcase.png)




The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: bad_case.v>
yosys> synth -top <name: bad_case>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> write_verilog -noattr bad_case_net.v
yosys> show
```

![](images/day5/badcase1.png)


The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to verilog model: ../mylib/verilog_model/sky130_fd_sc_hd.v> <name netlist: bad_case_net.v> <name testbench: tb_bad_case.v>
./a.out
gtkwave tb_bad_case.vcd
```


![](images/day5/badcasepost.png)


</details>


<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of mux_generate.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: mux_generate.v> <name testbench: tb_mux_generate.v>
./a.out
gtkwave tb_mux_generate.vcd
```




![](images/day5/muxgenerate.png)





The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: mux_generate.v>
yosys> synth -top <name: mux_generate>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> write_verilog -noattr mux_generate_net.v
yosys> show
```


![](images/day5/muxgenerate1.png)



The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to verilog model: ../mylib/verilog_model/sky130_fd_sc_hd.v> <name netlist: mux_generate_net.v> <name testbench: tb_mux_generate.v>
./a.out
gtkwave tb_mux_generate.vcd
```


![](images/day5/muxgeneratepost.png)

</details>
<details>
<summary> simulation , Synthesis, of demux_case.v </summary>


The Following commands are used to simulate the design 

```
iverilog <name verilog: demux_case.v> <name testbench: tb_demux_case.v>
./a.out
gtkwave tb_demux_case.vcd
```



![](images/day5/demuxcase.png)



Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: demux_case.v>
yosys> synth -top <name: demux_case>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> show
```

![](images/day5/demuxcase1.png)


</details>

<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of demux_generate.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: demux_generate.v> <name testbench: tb_demux_generate.v>
./a.out
gtkwave tb_demux_generate.vcd
```


![](images/day5/demuxgenerate.png)

The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: demux_generate.v>
yosys> synth -top <name: demux_generate>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> write_verilog -noattr mux_degenerate_net.v
yosys> show
```


![](images/day5/demuxgenerate1.png)



The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to verilog model: ../mylib/verilog_model/sky130_fd_sc_hd.v> <name netlist: demux_generate_net.v> <name testbench: tb_demux_generate.v>
./a.out
gtkwave tb_demux_generate.vcd
```


![](images/day5/demuxgeneratepost.png)
</details>




<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of rca.v </summary>





![](images/day5/WhatsApp%20Image%202023-08-15%20at%206.13.06%20PM%20(4).jpeg)



The Following commands are used to simulate the design 

```
iverilog <name verilog: rca.v> <name verilog: fa.v> <name testbench: tb_rca.v>
./a.out
gtkwave tb_rca.vcd
```



![](images/day5/rca.png)


The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: rca.v>
yosys> read_verilog <name of verilog file: fa.v>
yosys> synth -top <name: rca>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> write_verilog -noattr rca_net.v
yosys> show
```



![](images/day5/rca1.png)




The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to verilog model: ../mylib/verilog_model/sky130_fd_sc_hd.v> <name netlist: rca_net.v> <name testbench: tb_rca.v>
./a.out
gtkwave tb_rca.vcd
```


![](images/day5/rcapost.png)


</details>

### Day 6 

## Sequence Detector of "110101" using mealy machine.

<details>

<summary>Short Summary</summary>
In this design, I am going to detect the sequence “110101” using Mealy finite state machine.

</details>

<details>

<summary>Introduction</summary>
A Mealy machine is defined as a sequential network whose output is a function of both the present state and the input to the network. The state diagram for a Mealy machine has the output associated with the transition between states, as shown in the state diagram. Outputs are shown on transitions since they are determined in the same way as is the next state. In a Mealy machine, it may be possible to represent both combinations using the same state and to compute the single bit directly from the inputs. Hence, less states. With a Mealy machine, the outputs are computer from the state and current inputs and will not be ready for some time after the start of the clock cycle. It is capable of generating many different patterns of output signals for the same state, depending on the inputs present on the clock cycle.

![](images/day6/WhatsApp%20Image%202023-08-20%20at%207.57.09%20PM%20(1).jpeg)

![](images/day6/WhatsApp%20Image%202023-08-20%20at%207.57.09%20PM.jpeg)

</details>


<details>

<summary>pre-synthesis simulation , Synthesis, post-synthesis simulation of manhattan_sequencedetector.v </summary>

The Following commands are used to simulate the design 

```
iverilog <name verilog: manhattan_sequencedetector.v> <name testbench: tb_manhattan_sequencedetector.v>
./a.out
gtkwave manseqdector.vcd
```





![](images/day6/presynth.png)


The Following is Pre-synthesis simulation.

Use the following commands to synthesize the design.

```
yosys> read_liberty -lib <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> read_verilog <name of verilog file: manhattan_sequencedetector.v>
yosys> synth -top <name: manhattan_sequencedetector.v>
yosys> dfflibmap -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> abc -liberty <path to sky130_fd_sc_hd__tt_025C_1v80.lib>
yosys> opt_clean -purge
yosys> flatten
yosys> write_verilog -noattr manhattan_sequencedetector_net.v
yosys> show
```



![](images/day6/synth.png)

The gatelevel netlist of this design.

```
/* Generated by Yosys 0.31+13 (git sha1 411b6e98c, clang 10.0.0-4ubuntu1 -fPIC -Os) */

module manhattan_sequencedetector(sequence_in, clock, reset, detector_out);
  wire _00_;
  wire _01_;
  wire _02_;
  wire _03_;
  wire _04_;
  wire _05_;
  wire _06_;
  wire _07_;
  wire _08_;
  wire _09_;
  wire _10_;
  wire _11_;
  wire _12_;
  wire _13_;
  wire _14_;
  input clock;
  wire clock;
  wire [2:0] current_state;
  output detector_out;
  wire detector_out;
  wire [2:0] next_state;
  input reset;
  wire reset;
  input sequence_in;
  wire sequence_in;
  sky130_fd_sc_hd__clkinv_1 _15_ (
    .A(reset),
    .Y(_00_)
  );
  sky130_fd_sc_hd__nor2_1 _16_ (
    .A(current_state[1]),
    .B(current_state[0]),
    .Y(_04_)
  );
  sky130_fd_sc_hd__nor3b_1 _17_ (
    .A(current_state[1]),
    .B(current_state[0]),
    .C_N(current_state[2]),
    .Y(_05_)
  );
  sky130_fd_sc_hd__nor2_1 _18_ (
    .A(sequence_in),
    .B(_05_),
    .Y(_06_)
  );
  sky130_fd_sc_hd__and3b_1 _19_ (
    .A_N(current_state[1]),
    .B(current_state[0]),
    .C(current_state[2]),
    .X(_07_)
  );
  sky130_fd_sc_hd__o22ai_1 _20_ (
    .A1(sequence_in),
    .A2(_05_),
    .B1(_07_),
    .B2(_04_),
    .Y(_08_)
  );
  sky130_fd_sc_hd__nor2b_1 _21_ (
    .A(current_state[2]),
    .B_N(current_state[1]),
    .Y(_09_)
  );
  sky130_fd_sc_hd__nand2_1 _22_ (
    .A(current_state[0]),
    .B(_09_),
    .Y(_10_)
  );
  sky130_fd_sc_hd__nor3b_1 _23_ (
    .A(current_state[1]),
    .B(current_state[2]),
    .C_N(current_state[0]),
    .Y(_11_)
  );
  sky130_fd_sc_hd__o31ai_1 _24_ (
    .A1(sequence_in),
    .A2(current_state[2]),
    .A3(_04_),
    .B1(_08_),
    .Y(next_state[0])
  );
  sky130_fd_sc_hd__o21ai_0 _25_ (
    .A1(_05_),
    .A2(_11_),
    .B1(sequence_in),
    .Y(_12_)
  );
  sky130_fd_sc_hd__nand2_1 _26_ (
    .A(sequence_in),
    .B(current_state[0]),
    .Y(_13_)
  );
  sky130_fd_sc_hd__nand2_1 _27_ (
    .A(_09_),
    .B(_13_),
    .Y(_14_)
  );
  sky130_fd_sc_hd__nand2_1 _28_ (
    .A(_12_),
    .B(_14_),
    .Y(next_state[1])
  );
  sky130_fd_sc_hd__a21oi_1 _29_ (
    .A1(sequence_in),
    .A2(_10_),
    .B1(_06_),
    .Y(next_state[2])
  );
  sky130_fd_sc_hd__and3_1 _30_ (
    .A(sequence_in),
    .B(_00_),
    .C(_07_),
    .X(_03_)
  );
  sky130_fd_sc_hd__clkinv_1 _31_ (
    .A(reset),
    .Y(_01_)
  );
  sky130_fd_sc_hd__clkinv_1 _32_ (
    .A(reset),
    .Y(_02_)
  );
  sky130_fd_sc_hd__dfxtp_1 _33_ (
    .CLK(clock),
    .D(_03_),
    .Q(detector_out)
  );
  sky130_fd_sc_hd__dfrtp_1 _34_ (
    .CLK(clock),
    .D(next_state[0]),
    .Q(current_state[0]),
    .RESET_B(_00_)
  );
  sky130_fd_sc_hd__dfrtp_1 _35_ (
    .CLK(clock),
    .D(next_state[1]),
    .Q(current_state[1]),
    .RESET_B(_01_)
  );
  sky130_fd_sc_hd__dfrtp_1 _36_ (
    .CLK(clock),
    .D(next_state[2]),
    .Q(current_state[2]),
    .RESET_B(_02_)
  );
endmodule

```




![](images/day6/newstat.png)






The Following commands to used to perform Gate level simulation.

```
iverilog <path to verilog model: ../mylib/verilog_model/primitives.v> <path to verilog model: ../mylib/verilog_model/sky130_fd_sc_hd.v> <name netlist: manhattan_sequencedetector_net.v> <name testbench: tb_manhattan_sequencedetector.v>
./a.out
gtkwave manseqdectector.vcd
```

![](images/day6/postsynth.png)

Here we can confirm that there is no mismatch between the presynthesis and postsynthesis simulation.

</details>


### Day 7

<details>

<summary>Short Summary</summary>
In this module we learned about the basic of STA(Static Timing Analysis) and Delay of Cells ,Timings arcs , to develop constraints or SDC (Synopsys Design Constraints) Format . Analysis of .lib file like lut of delay model , unateness , power , area of different cells and to use tcl scripts to perform some activties on lib files.
 
</details>

<details>
<summary>Introduction to STA</summary>


Static timing analysis (STA) is a method of validating the timing performance of a design by checking all possible paths for timing violations. STA breaks a design down into timing paths, calculates the signal propagation delay along each path, and checks for violations of timing constraints inside the design and at the input/output interface.




![](images/day7/Day%207_230827_194115_1%20(1).jpg)

Here  let us consider given circuit  Max delay is given Tclk >= Tc-q + Tcomb +Tsetup. Let us consider the frequency of operation is 200 Mhz where time period is 5ns . Simplifying the equation we get  Tcomb = 5-Tc-q-Tsetup.


![](images/day7/Day%207_230827_194115_2%20(1).jpg)

Here water bucket analogy is used to understand the effects of delay in Digital Circuits 

We can conclude that Delay is f(input transition , output capacitance).



![](images/day7/Day%207_230827_194115_3%20(1).jpg)


As input transition increases delay increase.similarly when load capacitance increases the delay incereases vice versa.

</details>

<details>
<summary>Timing arcs</summary>

&nbsp;

There are two types of timings arcs:
1) Combinational Timing arc
2) Sequential Timing arc
![](images/day7/Day%207_230827_194115_4%20(1).jpg)

![](images/day7/Day%207_230827_194115_5%20(1).jpg)

</details>

<details>
<summary>Constraints</summary>
SDC is a short form of “Synopsys Design Constraint”. SDC is a common format for constraining the design which is supported by almost all Synthesis, PnR and other tools. Generally, timing, power and area constraints of design are provided through the SDC file and this file has extension .sdc. 

SDC file syntax is based on TCL format and all commands of sdc file follow the TCL syntax. In sdc file ‘#’ is used to comment a line and ” is used to break the line. SDC file can be generated by the synthesis tool and the same can be used in for PnR.



![](images/day7/Day%207_230827_194115_6%20(1).jpg)

Here path A is called  critical path .

![](images/day7/Day%207_230827_194115_7%20(1).jpg)


![](images/day7/Day%207_230827_194115_8%20(1).jpg)



![](images/day7/Day%207_230827_194115_9%20(1).jpg)


![](images/day7/Day%207_230827_194115_10%20(1).jpg)


![](images/day7/Day%207_230827_194115_11%20(1).jpg)


![](images/day7/Day%207_230827_194115_12%20(1).jpg)

</details>

<details>
<summary>Analysis of .lib file</summary>


![](images/day7/Day%207_230827_194115_13%20(1).jpg)



![](images/day7/Day%207_230827_194115_14%20(1).jpg)

![](images/day7/Day%207_230827_194115_15%20(1).jpg)

![](images/day7/Day%207_230827_194115_16%20(1).jpg)

</details>


<details>
<summary>some commands used in dc_shell</summary>

```
list_lib
```

```plaintext
foreach_in_collection <looping variable: my_lib_cell> [get_lib_cells */*and] {
set <another variable: my_lib_cell_name [get_object_name $my_lib_cell];
echo $my_lib_cell_name; 
}
```


```plaintext
get_lib_pins <path/librarycellnamefromprevcommand/*> 
foreach_in_collection <variable name: my_pins> [get_lib_pins <path/librarycellnamefromprevcommand/*>] {
set <another variable: my_pin_name [get_object_name $my_pins];
set <another variable: pin_dir> [get_lib_attribute $my_pin_name <attribute name:direction>];
echo $my_pin_name $pin_dir;
}
```

```plaintext
get_lib_attribute <nameoflibpinfromprevcommands> function
get_lib_attribute <nameofcellfromprevcommands> area
get_lib_attribute <nameoflibpinfromprevcommands> capacitance
get_lib_attribute <nameoflibpinfromprevcommands> clock
```

```plaintext
set <variable name: my_list> [list <path/librarycellnamefromprevcommand \
path/librarycellnamefromprevcommand\
path/librarycellnamefromprevcommand ]
foreach <variable name: my_cell> $my_list {
	foreach_in_collection <variable name: my_lib_pin> [get_lib_pins $(my_cell)/*] {
		set <variable name: my_lib_pin_name> [get_object_name $my_lib_pin];
		set <variable name: a> [get_lib_attribute $my_lib_pin_name direction];
		if { $a > 1 } {
			set <variable name: fn> [get_lib_attribute $my_lib_pin_name function];
			echo $my_lib_pin_name $a $fn;
		}
	}
```


```plaintext
get_lib_cells */* -filter "is_sequential == true"
```


```plaintext
list_attributes -app > <name: a>
```

</details>


### Day 8 


<details>
<summary>Summary</summary>

Here we have learnt to write sdc ( synopsys design constraints) for the design . There are lot constraints for clock , input ,output , internal paths, combinational paths.



![](images/day8/asic.png)

![](images/day8/2.png)

</details>


<details>
<summary>Clock modeling and SDC generation</summary>

![](images/day8/Day8_230828_184009_2.jpg)


![](images/day8/Day8_230828_184009_3.jpg)


![](images/day8/Day8_230828_184009_4.jpg)



![](images/day8/Day8_230828_184009_5.jpg)



![](images/day8/Day8_230828_184009_6.jpg)



![](images/day8/Day8_230828_184009_8.jpg)



![](images/day8/Day8_230828_184009_9.jpg)



![](images/day8/Day8_230828_184009_10.jpg)



![](images/day8/Day8_230828_184009_11.jpg)



![](images/day8/Day8_230828_184009_12.jpg)


![](images/day8/Day8_230828_184009_13.jpg)


![](images/day8/Day8_230828_184009_14.jpg)



![](images/day8/Day8_230828_184009_15.jpg)


![](images/day8/Day8_230828_184009_16.jpg)


![](images/day8/Day8_230828_184009_17.jpg)

</details>



### Day 9


<details>
<summary>Summary</summary>

I have written constraints for my design named it as manhattanseq_constraints.sdc to run opensta tool get the timing reports and manhattanscript.tcl to automate flow in opensta tool.

</details>

<details>
<summary>Constraints to my design </summary>


```plaintext

#clock constraints
create_clock -name clk1 -period 10 [get_ports clock] 
set_clock_latency -source 1 [get_clocks clk1]
set_clock_latency 2 [get_clocks clk1]
set_clock_uncertainty 0.6 [get_clocks clk1]
set_clock_uncertainty 0.1 [get_clocks clk1]

#input constraints
#set_driving_cell -lib_cell sky130_fd_sc_hd__buf_4 [all_inputs]
set_input_transition -max 0.4 [get_ports sequence_in]
set_input_transition -min 0.2 [get_ports sequence_in]
set_input_transition -max 0.4 [get_ports reset]
set_input_transition -min 0.2 [get_ports reset]
set_input_delay -max 3 -clock  clk1  [get_ports sequence_in]
set_input_delay -min 1 -clock  clk1  [get_ports sequence_in]
set_input_delay -max 3 -clock clk1   [get_ports reset]
set_input_delay -min 1 -clock  clk1 [get_ports reset]

#output constraints 
set_output_delay -max 3 -clock clk1 [get_ports detector_out]
set_output_delay -min 1  -clock  clk1 [get_ports detector_out]
set_load  -max 0.2 [get_ports detector_out]
set_load -min 0.05 [get_ports detector_out]
```

</details>

<details>
<summary>Insights of manhattanscript.tcl </summary>

```plaintext
read_liberty sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog manhattan_sequencedetector_net.v

link_design manhattan_sequencedetector

read_sdc manhattanseq_constraints.sdc

report_checks -fields {nets cap slew input_pins} -digits {5} > manhattan_timing.rpt
```
To run this script in opensta , we use this command 
```plaintext
sta manhattanscript.tcl
```

</details>


<details>
<summary>Timing reports </summary>


![](images/day8/Screenshot%202023-08-28%20220516.png)


</details>

### Day 10 

<details>
<summary> Summary </summary>

Here we are going to discuss of mos charactersitcs like Ids vs Vds , and all parameters related to it . Spice File are most important in delay analysis which gives all information  about the delay which used in Post CTS .
</details>

<details>
<summary> Intro. to SPICE </summary>


![](images/day10/Day%2010_230902_195133_1.jpg)

![](images/day10/Day%2010_230902_195133_2.jpg)

![](images/day10/Day%2010_230902_195133_3.jpg)

![](images/day10/Day%2010_230902_195133_4.jpg)

![](images/day10/Day%2010_230902_195133_5.jpg)

![](images/day10/Day%2010_230902_195133_6.jpg)

![](images/day10/Day%2010_230902_195133_7.jpg)

![](images/day10/Day%2010_230902_195133_8.jpg)

![](images/day10/Day%2010_230902_195133_9.jpg)

</details>

<details>
<summary> SPICE Syntax  </summary>

Following are the spice syntax file given to tools for simulation.


```plaintext
.LIB "<name: xxx>.mod" CMOS_MODELS
R<name> <1st node> <second node> <value>
M<name> <drain> <gate> <source> <bulk> <name in tech file> w=<value> L=<value>
V<name> <1st node> <second node> <value>
C<name> <1st node> <second node> <value>
```

```plaintext
.lib cmos_models
.Model <name that should match in netlist> NMOS (TOX = .. VTH0 = .. U0 = .. GAMMA1 = ..)
.Model <name that should match in netlist> PMOS (TOX = .. VTH0 = .. U0 = .. GAMMA1 = ..)
.endl
```

```plaintext
.<mode: dc> <voltage node to sweep: Vin> <start value: 0> <end value: 2.5> <steps: 0.1> <voltage node to sweep: Vdd> <start value: 0> <end value: 2.5> <steps: 2.5>
```


ngspice tool is used to view the simulation and follw the commands to get the output.

```plaintext
ngspice <spice file name>
plot -<name node>
```

</details>

<details>
<summary> SPICE Simulation : Lab 1  </summary>

```plaintext
ngspice <name: day1_nfet_idvds_L2_W5.spice>
plot -<name: vdd#branch>
```

![](images/day10/1.png)

</details>


### Day 11 

<details>
<summary> Summary  </summary>

Here we are going learn more about the mos charactersitcs.
</details>

<details>
<summary> Theory  </summary>


![](images/day%2011/day%2011_230903_192935_1.jpg)

![](images/day%2011/day%2011_230903_192935_2.jpg)

![](images/day%2011/day%2011_230903_192935_3.jpg)

![](images/day%2011/day%2011_230903_192935_4.jpg)

![](images/day%2011/day%2011_230903_192935_5.jpg)

![](images/day%2011/day%2011_230903_192935_6.jpg)

![](images/day%2011/day%2011_230903_192935_7.jpg)

![](images/day%2011/day%2011_230903_192935_8.jpg)

![](images/day%2011/day%2011_230903_192935_9.jpg)

</details>


<details>
<summary> Spice Simulation : Lab 2a  </summary>


```plaintext
ngspice <name: day2_nfet_idvds_L015_W039.spice>
plot -<name: vdd#branch>
```


![](images/day%2011/Screenshot%20from%202023-09-03%2016-48-17.png)

</details>


<details>
<summary> Spice Simulation : Lab 2b </summary>

```plaintext
ngspice <name: day2_nfet_idvgs_L015_W039.spice>
plot -<name: vdd#branch>
```


![](images/day%2011/2.png)

</details>

### Day 12

<details>
<summary> Summary </summary>

Here we are going to learn some more about the mos charactersitcs: Swtiching threshold and applications of it used in STA.

</details>


<details>
<summary> Theory</summary>


![](images/day12/Day12%20_230904_183541_1.jpg)


![](images/day12/Day12%20_230904_183541_2.jpg)


![](images/day12/Day12%20_230904_183541_3.jpg)

![](images/day12/Day12%20_230904_183541_4.jpg)

![](images/day12/Day12%20_230904_183541_5.jpg)


</details>

<details>
<summary> SPICE Simulation : Lab 3a </summary>


```plaintext
ngspice <name: day3_inv_vtc_Wp084_Wn036.spice>
plot <name: out> vs <name: in>
```


![](images/day12/1.png)

</details>

<details>
<summary> SPICE Simulation : Lab 3b </summary>


```plaintext
ngspice <name: day3_inv_tran_Wp084_Wn036.spice>
plot <name: out> vs <name: time> <name: in>
```


![](images/day12/2.png)

</details>


### Day 13 

<details>
<summary> Summary </summary>

Here we are going to learn some more about the CMOS Charactersitcs : Noise Margin
</details>

<details>
<summary> Theory </summary>



![](images/day13/Day%2013_230905_104715_1.jpg)


![](images/day13/Day%2013_230905_104715_2.jpg)


![](images/day13/Day%2013_230905_104715_3.jpg)


![](images/day13/Day%2013_230905_104715_4.jpg)

</details>

<details>
<summary> SPICE Simulation : Lab 4 </summary>

```plaintext
ngspice <name: day4_inv_noisemargin_wp1_wn036.spice>
plot <name: out> vs <name: in>
```


![](images/day13/1.png)

Here Noise Margin low is 0.63 V and Noise Margin High is 0.71 V

</details>


### Day 14

<details>
<summary> Summary </summary>

Here we are going to learn CMOS charactersitcs : Supply Variation , Device Variation like process variation and gate oxide variation and its effects .

</details>

<details>
<summary> Theory </summary>


![](images/day14/Day%2014_230906_112102_1.jpg)

![](images/day14/Day%2014_230906_112102_2.jpg)


![](images/day14/Day%2014_230906_112102_3.jpg)


![](images/day14/Day%2014_230906_112102_4.jpg)


![](images/day14/Day%2014_230906_112102_5.jpg)

</details>

<details>
<summary> SPICE Simulation: Lab 5a </summary>

```plaintext
ngspice <name: day5_inv_supplyvariation_Wp1_Wn036.spice>
```


![](images/day14/1.png)


![](images/day14/2.png)

For 1.8 v Gain is |(1.75-0.11/0.71-0.97)| = 1.65/0.26 = 6.33  

For 0.8 v Gain is |(0.77-0.02)/(0.42-0.50)| =
0.75/0.08 = 9.375

Here we can observe that in lower nodes decrease in supply voltage leads to increase in Gain but performance decreases.

</details>


<details>
<summary> SPICE Simulation: Lab 5b </summary>

```plaintext
ngspice <name: day5_inv_devicevariation_wp7_wn042.spice>
plot <name: out> vs <name: in>
```

![](images/day14/3.png)


![](images/day14/4.png)

Even though their is a larger varaition in the device that is width changes due to process variation but their is less impact on the saturation voltage point . Ideally 0.9 v is Vsat but now its 0.989 v . 

We can conclude that CMOS is robust.

</details>

### Day 15

<details>
<summary> Summary  </summary>

Here we are going to learn about the PVT Corner their imapct on delay and performance.

</details>

<details>
<summary> Theory  </summary>

PVT:
PVT is the Process, Voltage, and Temperature. In order to make our chip to work after fabrication in all the possible conditions, we simulate it at different corners of process, voltage, and temperature. These conditions are called corners. All these three parameters directly affect the delay of the cell.

Process:

There are millions of transistors on the single-chip as we are going to lower nodes and all the transistors in a chip cannot have the same properties. Process variation is the deviation in parameters of the transistor during the fabrication.

During manufacturing a die, the area at the center and at the boundary will have different process variations. This happens because layers that will be getting fabricated cannot be uniform all over the die.

Below are a few important factors which can cause the process variation;

1. Oxide thickness variation
2. Dopant and mobility fluctuation
3. Transistor width
4. RC Variation
5. channel length
6. doping concentration,
7. metal thickness
8. impurity concentration densities
9. diffusion depths
10. imperfections in the manufacturing process like mask print, etching


![](images/day16/process.JPG)

Voltage:



As we are going to the lower nodes the supply voltage for a chip is also going to less. Let’s say the chip is operating at 1.2V. So, there are chances that at certain instances of time this voltage may vary. It can go to 1.5V or 0.8V. To take care of this scenario, we consider voltage variation.

There are multiple reasons for voltage variation.

1. IR drop is caused by the current flow over the power grid network. 
2. Supply noise caused by parasitic inductance in combination with resistance and capacitance. when the current is flowing through parasitic inductance (L) it will causes the voltage bounce.



![](images/day16/voltage.JPG)

Temperature:


The transistor density is not uniform throughout the chip. Some regions of the chip have higher density and higher switching, resulting in higher power dissipation and Some regions of the chip have lower density and lower switching, resulting in lower power dissipation Hence the junction temperature at these regions may be higher or lower depending upon the density of transistors. Because of the variation in temperature across the chip, it introduces different delays across all the transistors.

For Technology node below 65nm there exsists "temperature inversion" where delay increases in decrease in temperature.

![](images/day16/temp.JPG)


```plaintext
Best case: fast process, highest voltage and lowest temperature

Worst case: slow process, lowest voltage and highest temperature
```

</details>


### Day 16 

Post Synthesis for my Design

<details>
<summary> Summary  </summary>

Here we are going to perform post Synthesis STA for different corner say ss,ff,tt and find the changes in WNS(Worst Negative Slack), WHS(Worst Hold Slack), TNS(Total Negative Slack).

The library files are derived from https://github.com/Geetima2021/vsdpcvrd

There are 
* 1 tt (typical typical) Library
* 5 ff (fast fast) Library
* 7 ss (slow slow) Library

with different combinations of Voltage and Temperature.

</details>

<details>
<summary> Constraints</summary>

Here I am using the same constraints which I used for first STA.


```plaintext
#clock constraints
create_clock -name clk1 -period 10 [get_ports clock] 
set_clock_latency -source 1 [get_clocks clk1]
set_clock_latency 2 [get_clocks clk1]
set_clock_uncertainty 0.6 [get_clocks clk1]
set_clock_uncertainty 0.1 [get_clocks clk1]

#input constraints
#set_driving_cell -lib_cell sky130_fd_sc_hd__buf_4 [all_inputs]
set_input_transition -max 0.4 [get_ports sequence_in]
set_input_transition -min 0.2 [get_ports sequence_in]
set_input_transition -max 0.4 [get_ports reset]
set_input_transition -min 0.2 [get_ports reset]
set_input_delay -max 3 -clock  clk1  [get_ports sequence_in]
set_input_delay -min 1 -clock  clk1  [get_ports sequence_in]
set_input_delay -max 3 -clock clk1   [get_ports reset]
set_input_delay -min 1 -clock  clk1 [get_ports reset]

#output constraints 
set_output_delay -max 3 -clock clk1 [get_ports detector_out]
set_output_delay -min 1  -clock  clk1 [get_ports detector_out]
set_load  -max 0.2 [get_ports detector_out]
set_load -min 0.05 [get_ports detector_out]
```
</details>


<details>
<summary> Execution</summary>

I written three different scripts for sta anaysis ran in OpenSTA tool to analyse the outputs.

```plaintext
sta manhattanscript_tt.tcl
sta manhattanscript_ff.tcl
sta manhattanscript_ss.tcl
```
Below are the findings of the results.



![](images/day16/timngchart.jpg)



![](images/day16/1.png)


here we can observe at the ss ,at lowest temperature and lowest voltage we can observe the worst case of delay which is the worst PVT corner Combination.

</details>


### Day 17 

<details>
<summary> Summary  </summary>

Here we are going to lesrn the automated flow of PnR (Placement and Routing) using openlane script which comprises of different open source tools combined to together a single script.

A ASIC Design consists of pdk , RTL , EDA Tools to be open sourced to create a community intiaited by eFabless.

</details>

<details>
<summary> Theory </summary>


![](images/day17/Day17_230916_135214_1.jpg)


![](images/day17/Day17_230916_135214_2.jpg)


![](images/day17/Day17_230916_135214_3.jpg)


![](images/day17/Day17_230916_135214_4.jpg)


![](images/day17/Day17_230916_135214_5.jpg)


![](images/day17/Day17_230916_135214_6.jpg)


![](images/day17/1.png)


![](images/day17/2.png)

</details>

<details>
<summary> Installation of OpenLane </summary>

```plaintext
sudo apt-get update
sudo apt-get upgrade
sudo apt install -y build-essential python3 python3-venv python3-pip make git
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world
sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot
```
```plaintext
git --version
docker --version
python3 --version
python3 -m pip --version
make --version
python3 -m venv -h
```
```plaintext
cd $HOME
git clone https://github.com/The-OpenROAD-Project/OpenLane
cd OpenLane
make
make test
```
</details>

<details>
<summary> Tutorial : Picorv32a (Synthesis) </summary>

```plaintext
make mount
./flow.tcl -interactive
package require openlane 0.9
```


```plaintext
run_synthesis
```


![](images/day17/3.png)


![](images/day17/4.png)

```plaintext
Flop ratio = # of D Flipflops / Total # of cells
dfxtp_2 = 1596,
Number of cells = 10206,
Flop ratio = 1596/10206 = 0.1537 = 15.37%
```
	
	
![](images/day17/7.png)

```plaintext
============================================================================
======================= Typical Corner ===================================

Startpoint: _17318_ (rising edge-triggered flip-flop clocked by clk)
Endpoint: _17318_ (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                  0.15    0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock network delay (ideal)
                  0.15    0.00    0.00 ^ _17318_/CLK (sky130_fd_sc_hd__dfxtp_2)
                  0.07    0.35    0.35 ^ _17318_/Q (sky130_fd_sc_hd__dfxtp_2)
     3    0.01                           count_instr[63] (net)
                  0.07    0.00    0.35 ^ _13104_/A1 (sky130_fd_sc_hd__a21oi_2)
                  0.02    0.04    0.40 v _13104_/Y (sky130_fd_sc_hd__a21oi_2)
     1    0.00                           _00644_ (net)
                  0.02    0.00    0.40 v _17318_/D (sky130_fd_sc_hd__dfxtp_2)
                                  0.40   data arrival time

                  0.15    0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock network delay (ideal)
                          0.25    0.25   clock uncertainty
                          0.00    0.25   clock reconvergence pessimism
                                  0.25 ^ _17318_/CLK (sky130_fd_sc_hd__dfxtp_2)
                         -0.01    0.24   library hold time
                                  0.24   data required time
-----------------------------------------------------------------------------
                                  0.24   data required time
                                 -0.40   data arrival time
-----------------------------------------------------------------------------
                                  0.16   slack (MET)
```


</details>


### Day 18


<details>
<summary> Summary  </summary>

Here we are going to learn the basics of Floorplanning and Placement Stage and implement this in the openlane flow for picorv32a design.

</details>


<details>
<summary> Theory </summary>


![](images/day18/Day18_230919_161103_1.jpg)


![](images/day18/Day18_230919_161103_2.jpg)


![](images/day18/Day18_230919_161103_3.jpg)


![](images/day18/Day18_230919_161103_4.jpg)


![](images/day18/Day18_230919_161103_5.jpg)


![](images/day18/Day18_230919_161103_6.jpg)


![](images/day18/Day18_230919_161103_7.jpg)


![](images/day18/Day18_230919_161103_8.jpg)


![](images/day18/Day18_230919_161103_9.jpg)


![](images/day18/Day18_230919_161103_10.jpg)


![](images/day18/Day18_230919_161103_11.jpg)


![](images/day18/Day18_230919_161103_12.jpg)


![](images/day18/Day18_230919_161103_13.jpg)


![](images/day18/Day18_230919_161103_14.jpg)


![](images/day18/Day18_230919_161103_15.jpg)


![](images/day18/Day18_230919_161103_16.jpg)

</details>

<details>
<summary> Tutorial : Picorv32a (Floorplanning , Placement) </summary>



Follow these commands to get the floorplanning.

```plaintext
run_floorplan
```

To view the floorplan use magic tool and follow these commands

```plaintext
magic -T $PDK_ROOT/$PDK/libs.tech/magic/sky130.tech
```


and in the tool window type these commands

```plaintext
lef read ../../tmp/merged.min.lef
def read picorv32.def
```


![](images/day18/1.png)


![](images/day18/2.png)

Here the cells of the netlist are in bottom left corner of core area that means they are placed inside the core area. Next step is to do the placement step arrange these cells in the core area.

To do the placement type these commands

```plaintext
run_placement
```

To view the placement view use magic tool.

```plaintext
magic -T $PDK_ROOT/$PDK/libs.tech/magic/sky130.tech
```

```plaintext
lef read ../../tmp/merged.min.lef
def read picorv32.def
```


![](images/day18/3.png)


![](images/day18/4.png)

</details>


### Day 19

<details>
<summary> Summary  </summary>

Here we are going to learn the basics of cmos level charactersitcs and the drc checking using magic tool. 

</details>

<details>
<summary> Theory </summary>


![](images/day19/Day%2019_230920_161829_1.jpg)

![](images/day19/Day%2019_230920_161829_2.jpg)


![](images/day19/16mask.png)


</details>

<details>
<summary> magic: inverter </summary>

```plaintext
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

```plaintext
magic -T sky130A.tech sky130_inv.mag &
```


![](images/day19/1.png)

spice extraction steps

```plaintext
extract all
ext2spice cthresh 0 rethresh 0
ext2spice
```

output of spice netlist

```plaintext
.include ./libs/pshort.lib
.include ./libs/nshort.lib
VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
.tran 1n 20n
.control 
run
.endc
.end
```


![](images/day19/2.png)

convert the ext file to spice file and use ngspice tool to run the simulation.

```plaintext
ngspice sky130_inv.spice
plot <output: y> vs time <input: a>
```


![](images/day19/3.png)

![](images/day19/4.png)

```plaintext
Rise transition = (2.1957e-9 - 2.15099e-9) = 44.71 ps
Fall transition = (4.06461e-9 - 4.03953e-9) = 25.08 ps
Cell rise delay = (2.17613e-9 - 2.15e-9) = 26.13 ps
Cell fall delay = (4.05241e-9 - 4.04991e-9) = 25.00 ps
```
</details>
<details>
<summary> drc checks</summary>

```plaintext
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
```

```plaintext
magic -d XR
```


![](images/day19/5.png)

```plaintext
load poly.mag
```


![](images/day19/6.png)


![](images/day19/7.png)

```plaintext
tech load sky130A.tech
drc check
```


![](images/day19/8.png)



![](images/day19/9.png)

```plaintext
tech load sky130A.tech
drc check
```


![](images/day19/10.png)


```plaintext
load nwell.mag
```


![](images/day19/11.png)


![](images/day19/12.png)

```plaintext
tech load sky130A.tech
drc check
drc style drc(full)
drc check
```

![](images/day19/13.png)


![](images/day19/14.png)

</details>



### Day 20

<details>
<summary> Summary  </summary>

Here we are going to learn the lef writing , integrating the custom cells to the exisisting design, post cts and pre cts of picorv32a design.

</details>

<details>
<summary> Theory  </summary>


![](images/day20/Day20_230921_233326_1.jpg)

![](images/day20/Day20_230922_153153_2.jpg)


![](images/day20/Day20_230922_153153_3.jpg)


![](images/day20/Day20_230922_153153_4.jpg)


![](images/day20/Day20_230922_153153_5.jpg)


![](images/day20/Day20_230922_153153_6.jpg)


![](images/day20/Day20_230922_153153_7.jpg)


</details>


<details>
<summary> Openlane: Lef  Extraction </summary>

```plaintext
vi /home/sai/Desktop/open_pdks/sky130/sky130A/libs.tech/openlane/sky130_fd_sc_hd/tracks.info
```

![](images/day20/2.png)

```plaintext
grid 0.46um 0.34um 0.23um 0.17um
save sky130_vsdinv.mag
```

```plaintext
magic -T sky130A.tech sky130_vsdinv.mag &
```

```plaintext
lef write
```


![](images/day20/3.png)

```plaintext
cp /home/sai/OpenLane/vsdstdcelldesign/sky130_vsdinv.lef . 
cp /home/sai/OpenLane/vsdstdcelldesign/libs/sky130_fd_sc_hd__typical.lib .
cp /home/sai/OpenLane/vsdstdcelldesign/libs/sky130_fd_sc_hd__fast.lib .
cp /home/sai/OpenLane/vsdstdcelldesign/libs/sky130_fd_sc_hd__slow.lib .
```

save the lib , lef file to openlane designs folder to integrate the standard cells to the designs.

then modify the config.json in the design folder to run with the updated changes.


![](images/day20/4.png)

use the following commands to run and view the design 

```plaintext
exit
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
run_floorplan
run_placement
```

```plaintext
magic read -T /home/sai/Desktop/open_pdks/sky130/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.max.lef def read picorv32.def &
```


![](images/day20/1.png)

here we can see vsdinv cell is added to picorv32a design this confirm the flexibililty to create the custom libs and added to current design.


</details>

<details>
<summary> Tutorial:Picorv32a (CTS)</summary>

```plaintext
run_cts
```

use the following command to run the cts in tool and output generates the reports can found in the log directory.



![](images/day20/6.png)


![](images/day20/7.png)


![](images/day20/8.png)


![](images/day20/9.png)

</details>

### Day 21

<details>
<summary> Summary  </summary>

Here we are going to learn routing concepts and appply it in the openlane flow and learn about triton route Tool and analyse the outputs .

</details>

<details>
<summary> Theory </summary>


![](images/day21/Day%2021_230922_165631_1.jpg)


![](images/day21/Day%2021_230922_165631_2.jpg)


![](images/day21/Day%2021_230922_165631_3.jpg)


![](images/day21/Day%2021_230922_165631_4.jpg)


![](images/day21/Day%2021_230922_165631_5.jpg)


![](images/day21/Day%2021_230922_165631_6.jpg)


![](images/day21/Day%2021_230922_165631_7.jpg)

![](images/day21/Day%2021_230922_165631_8.jpg)

![](images/day21/Day%2021_230922_165631_9.jpg)

</details>

<details>
<summary> Tutorial:Picorv32a (PDN and Routing)</summary>


```plaintext
gen_pdn
echo $::env(CURRENT_DEF)
```

```plaintext
run_routing
```


![](images/day21/1.png)


![](images/day21/2.png)

```plaintext
run_parasitics_sta
run_irdrop_report
run_magic
run_magic_spice_export
run_lvs
run_magic_drc
run_antenna_check
run_erc
```


![](images/day21/3.png)


![](images/day21/4.png)

This is final flow and layout after the routing stage.

</details>



### Day 22


<details>
<summary> Summary  </summary>

Here we are going to integrate my design to the Openlane flow and running the script to perform from RTLTOGDS and output is integrate to caravel core and given to tapeout shuttle.

</details>

<details>
<summary> Synthesis:ManhattanDetector</summary>

To avoid error in PDN stage we need to use the following command in the json file.

```plaintext
"FP_PDN_AUTO_ADJUST": 0
```
```plaintext
make mount
./flow.tcl -interactive
prep -design manhattandetector

run_synthesis
```

![](images/day%2022/1.png)

```plaintext
Here FlipFlop Ratio = no of filpflops/ Total number of cells

4/21 = 0.190 * 100 = 19 %
```

The sta reports are also generated in this stage and constraints provided to tool is similar which we have used eariler in opensta tool and we got similar reports using this tool.


![](images/day%2022/2.png)


![](images/day%2022/3.png)



![](images/day%2022/4.png)


![](images/day%2022/5.png)

![](images/day%2022/6.png)
![](images/day%2022/7.png)


</details>

<details>
<summary> Floorplan:ManhattanDetector</summary>

```plaintext
run_floorplan
```

To view the floorplan in magic use the following commands

```plaintext
magic read -T /home/sai/Desktop/open_pdks/sky130/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.min.lef def read manhattandetector.def &
```


![](images/day%2022/8.png)

![](images/day%2022/9.png)

</details>

<details>
<summary> Placement:ManhattanDetector</summary>

```plaintext
run_placement
```
To view the placement in magic use the following commands 

```plaintext
magic read -T /home/sai/Desktop/open_pdks/sky130/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.max.lef def read manhattandetector.def &
```

![](images/day%2022/10.png)

![](images/day%2022/11.png)

</details>

<details>
<summary> CTS:ManhattanDetector</summary>

```plaintext
run_cts
```


![](images/day%2022/12.png)

![](images/day%2022/13.png)


![](images/day%2022/14.png)


![](images/day%2022/15.png)


![](images/day%2022/16.png)


![](images/day%2022/17.png)

</details>

<details>
<summary> Routing:ManhattanDetector</summary>

```plaintext
gen_pdn
run_routing
```


![](images/day%2022/18.png)

![](images/day%2022/19.png)


![](images/day%2022/20.png)


![](images/day%2022/21.png)

</details>

<details>
<summary> GDSII:ManhattanDetector</summary>

```plaintext
run_parasitics_sta
run_irdrop_report
run_magic
run_magic_spice_export
run_lvs
run_magic_drc
run_antenna_check
run_erc
```


To view final layout use the following command

```plaintext
magic
```

In GUI window select file > open > .mag file  



![](images/day%2022/23.png)


![](images/day%2022/24.png)


![](images/day%2022/27.png)

</details>

<details>
<summary> Non Interactive mode :ManhattanDetector</summary>


```plaintext
OpenLane Container (7e5a2e9):/openlane$ ./flow.tcl -design manahattandetector
OpenLane 7e5a2e9fb274c0a100b4859a927adce7089455ff
All rights reserved. (c) 2020-2022 Efabless Corporation and contributors.
Available under the Apache License, version 2.0. See the LICENSE file for more details.

[INFO]: Using configuration in 'designs/manahattandetector/config.json'...
[INFO]: PDK Root: /home/sai/.volare
[INFO]: Process Design Kit: sky130A
[INFO]: Standard Cell Library: sky130_fd_sc_hd
[INFO]: Optimization Standard Cell Library: sky130_fd_sc_hd
[INFO]: Run Directory: /openlane/designs/manahattandetector/runs/RUN_2023.09.23_22.57.58
[INFO]: Saving runtime environment...
[INFO]: Preparing LEF files for the nom corner...
[INFO]: Preparing LEF files for the min corner...
[INFO]: Preparing LEF files for the max corner...
[INFO]: Running linter (Verilator) (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/synthesis/linter.log)...
[INFO]: 0 errors found by linter
[WARNING]: 3 warnings found by linter
[STEP 1]
[INFO]: Running Synthesis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/synthesis/1-synthesis.log)...
[STEP 2]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/synthesis/2-sta.log)...
[STEP 3]
[INFO]: Running Initial Floorplanning (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/floorplan/3-initial_fp.log)...
[INFO]: Floorplanned with width 30.36 and height 29.92.
[STEP 4]
[INFO]: Running IO Placement...
[STEP 5]
[INFO]: Running Global Placement (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/5-global.log)...
[STEP 6]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/6-gpl_sta.log)...
[STEP 7]
[INFO]: Running basic macro placement (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/7-basic_mp.log)...
[STEP 8]
[INFO]: Running Tap/Decap Insertion (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/floorplan/8-tap.log)...
[INFO]: Power planning with power {VPWR} and ground {VGND}...
[STEP 9]
[INFO]: Generating PDN (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/floorplan/9-pdn.log)...
[STEP 10]
[INFO]: Running Global Placement (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/10-global.log)...
[STEP 11]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/11-gpl_sta.log)...
[STEP 12]
[INFO]: Running Placement Resizer Design Optimizations (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/12-resizer.log)...
[STEP 13]
[INFO]: Running Detailed Placement (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/13-detailed.log)...
[STEP 14]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/placement/14-dpl_sta.log)...
[STEP 15]
[INFO]: Running Clock Tree Synthesis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/cts/15-cts.log)...
[STEP 16]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/cts/16-cts_sta.log)...
[STEP 17]
[INFO]: Running Placement Resizer Timing Optimizations (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/cts/17-resizer.log)...
[STEP 18]
[INFO]: Running Global Routing Resizer Design Optimizations (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/18-resizer_design.log)...
[STEP 19]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/19-rsz_design_sta.log)...
[STEP 20]
[INFO]: Running Global Routing Resizer Timing Optimizations (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/20-resizer_timing.log)...
[STEP 21]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/21-rsz_timing_sta.log)...
[STEP 22]
[INFO]: Running I/O Diode Insertion (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/22-io_diodes.log)...
[STEP 23]
[INFO]: Running Detailed Placement (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/23-io_diode_legalization.log)...
[STEP 24]
[INFO]: Running Heuristic Diode Insertion (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/24-diodes.log)...
[STEP 25]
[INFO]: Running Detailed Placement (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/25-diode_legalization.log)...
[STEP 26]
[INFO]: Running Global Routing (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/26-global.log)...
[INFO]: Starting OpenROAD Antenna Repair Iterations...
[STEP 27]
[INFO]: Writing Verilog (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/26-global_write_netlist.log)...
[STEP 28]
[INFO]: Running Single-Corner Static Timing Analysis (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/28-grt_sta.log)...
[STEP 29]
[INFO]: Running Fill Insertion (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/29-fill.log)...
[STEP 30]
[INFO]: Running Detailed Routing (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/30-detailed.log)...
[INFO]: No DRC violations after detailed routing.
[STEP 31]
[INFO]: Checking Wire Lengths (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/routing/31-wire_lengths.log)...
[STEP 32]
[INFO]: Running SPEF Extraction at the min process corner (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/32-parasitics_extraction.min.log)...
[STEP 33]
[INFO]: Running Multi-Corner Static Timing Analysis at the min process corner (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/33-rcx_mcsta.min.log)...
[STEP 34]
[INFO]: Running SPEF Extraction at the max process corner (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/34-parasitics_extraction.max.log)...
[STEP 35]
[INFO]: Running Multi-Corner Static Timing Analysis at the max process corner (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/35-rcx_mcsta.max.log)...
[STEP 36]
[INFO]: Running SPEF Extraction at the nom process corner (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/36-parasitics_extraction.nom.log)...
[STEP 37]
[INFO]: Running Multi-Corner Static Timing Analysis at the nom process corner (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/37-rcx_mcsta.nom.log)...
[WARNING]: Module sky130_fd_sc_hd__tapvpwrvgnd_1 blackboxed during sta
[WARNING]: Module sky130_ef_sc_hd__decap_12 blackboxed during sta
[WARNING]: Module sky130_fd_sc_hd__fill_1 blackboxed during sta
[WARNING]: Module sky130_fd_sc_hd__fill_2 blackboxed during sta
[STEP 38]
[INFO]: Creating IR Drop Report (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/38-irdrop.log)...
[WARNING]: VSRC_LOC_FILES is not defined. The IR drop analysis will run, but the values may be inaccurate.
[STEP 39]
[INFO]: Running Magic to generate various views...
[INFO]: Streaming out GDSII with Magic (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/39-gdsii.log)...
[INFO]: Generating MAGLEF views...
[INFO]: Generating lef with Magic (/openlane/designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/39-lef.log)...
[STEP 40]
[INFO]: Streaming out GDSII with KLayout (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/40-gdsii-klayout.log)...
[STEP 41]
[INFO]: Running XOR on the layouts using KLayout (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/41-xor.log)...
[INFO]: No XOR differences between KLayout and Magic gds.
[STEP 42]
[INFO]: Running Magic Spice Export from LEF (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/42-spice.log)...
[STEP 43]
[INFO]: Writing Powered Verilog (logs: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/43-write_powered_def.log, designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/43-write_powered_verilog.log)...
[STEP 44]
[INFO]: Writing Verilog (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/43-write_powered_verilog.log)...
[STEP 45]
[INFO]: Running LVS (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/45-lvs.lef.log)...
[STEP 46]
[INFO]: Running Magic DRC (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/46-drc.log)...
[INFO]: Converting Magic DRC database to various tool-readable formats...
[INFO]: No DRC violations after GDS streaming out.
[STEP 47]
[INFO]: Running OpenROAD Antenna Rule Checker (log: designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/logs/signoff/47-arc.log)...
[INFO]: Saving current set of views in 'designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/results/final'...
[INFO]: Saving runtime environment...
[INFO]: Generating final set of reports...
[INFO]: Created manufacturability report at 'designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/reports/manufacturability.rpt'.
[INFO]: Created metrics report at 'designs/manahattandetector/runs/RUN_2023.09.23_22.57.58/reports/metrics.csv'.
[INFO]: There are no max slew, max fanout or max capacitance violations in the design at the Typical corner.
[INFO]: There are no hold violations in the design at the Typical corner.
[INFO]: There are no setup violations in the design at the Typical corner.
[SUCCESS]: Flow complete.
[INFO]: Note that the following warnings have been generated:
[WARNING]: 3 warnings found by linter
[WARNING]: Module sky130_fd_sc_hd__tapvpwrvgnd_1 blackboxed during sta
[WARNING]: Module sky130_ef_sc_hd__decap_12 blackboxed during sta
[WARNING]: Module sky130_fd_sc_hd__fill_1 blackboxed during sta
[WARNING]: Module sky130_fd_sc_hd__fill_2 blackboxed during sta
[WARNING]: VSRC_LOC_FILES is not defined. The IR drop analysis will run, but the values may be inaccurate.
```

</details>




























































































