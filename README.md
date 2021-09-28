
# Advanced RTL Synthesis and STA on Synopsys DC using Sky130 library file
![Advanced Synthesis and STA with DC-1536x864](https://user-images.githubusercontent.com/73732594/135170186-1fe95fdb-7864-4bb9-a6c3-440e1e58e1c3.png)

**Contents:**

<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=6 orderedList=false`} -->

<!-- code_chunk_output -->

- [Day-1](#day-1)
	- [Introduction](#introduction)
	- [DC_shell](#dc_shell)
	- [Tcl_scripting](#tcl_scripting)
	- [Design_Vision](#design_vision)
- [Day-2](#day-2)
  - [STA_Basics](#sta_basics)
  - [Constraints_and_IO_load](#constraints_and_io_load)
  - [Timing_and_Exploring_dot_lib_files](#timing_and_exploring_dot_lib_files)  
- [Day-3](#day-3)
  - [Clock-tree_modelling](#clock-tree_modelling)
  - [Clock-network_modelling_with_IO_delays](#clock-network_modelling_with_io_delays)
- [Day-4](#day-4)
  - [Combinational_and_resource_sharing_optimization](#combinational_and_resource_sharing_optimization)
  - [Sequential_optimization](#sequential_optimization)
  - [Special_optimization](#special_optimization)
- [Day-5](#day-5)
  - [Timing_report](#timing_report)
  - [Set_max_capacitance_HFN](#set_max_capacitance_hfn)
  - [Summary](#summary)
  
 


- [Acknowledgements:](#acknowledgements)
- [References:](#references)

<!-- /code_chunk_output -->

---



# Day-1
## Introduction

There are various aspects of synthesis which are needed to be taken care beyond getting successful compilation and this workshop ensured we will get to know that and more. We started off with an introduction to invoking DC shell and, then Design Vision for a GUI flavor. Since the shell environment employs TCL commands, we were given a primer on utilising it in general and especially in Synopsys environment with an introduction to Synopsys propietary file formats, data structures and TCL commands. There are various flavours of the cells provided as for setup constraints you might need a performance oriented or fast implementation, but to take care of hold violations, you might require a slower variant of the same.

## DC_shell

Here we realize as seen in the snippet, that Synopsys already loads a dummy library for mapping some standard designs using their propietary GTECH library, so that the user in question, gets some results out of synthesizing the design. 

![Screenshot (313)](https://user-images.githubusercontent.com/73732594/135148283-1c600533-50ef-4783-bb83-339bda1598f9.png)

![lab1_your_library](https://user-images.githubusercontent.com/73732594/135129831-4f2a1c15-881c-444d-b1b4-13e920fe3551.JPG)

![lab1_withoutsky_dff](https://user-images.githubusercontent.com/73732594/135132561-b0a6ee0f-186c-41dc-9de2-6f16739c336b.JPG)

![lab1_dff_gtech](https://user-images.githubusercontent.com/73732594/135132676-9fe61b80-336b-4829-b2cb-1e0728b0ddca.JPG)

![Screenshot (206)](https://user-images.githubusercontent.com/73732594/135165942-5b405af2-459a-47cb-a807-ad0076e7608c.png)


Since Synopsys doesn't support .lib files, we have to provide it with .db library file, the library file in question being the sky130 for typical corner (tt) at 1.8V nominal voltage, has been read here.

![lab1_inputting db file](https://user-images.githubusercontent.com/73732594/135133097-bf3037c4-a6d0-4052-828b-53c702a2bde1.JPG)

The target library is set, which is then linked with tool memory, which is used for further linking design with it.

![lab1_linking_dff](https://user-images.githubusercontent.com/73732594/135139721-3bb2edf8-cd04-4b3d-b2cd-d36cb5b39a4e.JPG)

Linking and compiling yields the following on the terminal.

![lab1_compiling_dff_mapping](https://user-images.githubusercontent.com/73732594/135139779-9fdb0dc9-033c-421e-a90f-08613fbc9841.JPG)

![lab1_compiling_dff_delay area](https://user-images.githubusercontent.com/73732594/135139850-2032df1b-605c-485d-97dc-a800311e7b13.JPG)

As per our instructor, here you can see the beauty, 😄, the mapped netlist as per the following snippet, after writing the netlist out after compilation.

![lab1_sky130_dff_verilog_netlist](https://user-images.githubusercontent.com/73732594/135140723-ba414498-fdaa-40ec-9e2d-1081ed9dee29.JPG)

## Design_Vision

Since, we have now acquired the netlist, it is natural to have curiosity to see the schematic. For that we invoke Design Vision, which provides a Graphical User Interface to the user for viewing the resultant schematic, for which we create a .ddc file, which consists of all the commands and the library used there, before creating the .ddc file. Also, we have to make note that our synthesized design might involve an inverter in input for an active-low input, and although that is not a significant change, it topogrphically does represent a change.

![lab2_design_vision_gui](https://user-images.githubusercontent.com/73732594/135141827-42dbe68a-9a1d-449c-892f-1e609b399714.JPG)

![Screenshot (317)](https://user-images.githubusercontent.com/73732594/135148588-da8069aa-9360-44a0-9ab5-d01b3b250986.png)

![Screenshot (318)](https://user-images.githubusercontent.com/73732594/135148609-a073f2f6-aa64-4607-a56f-4112531bfaae.png)

![Screenshot (322)](https://user-images.githubusercontent.com/73732594/135148851-19049d17-bd8a-4297-aadb-11450fc697e2.png)


It is important for us to configure our own .synopsys_dc.setup file so that repetitive tasks like associating a target and link library is taken care of. We need to create it in home directory of the terminal, which is an important and necessary step. 

![lab3_synopsysdcsetup](https://user-images.githubusercontent.com/73732594/135142185-c791fe6c-6748-4a68-b038-6638a4efe7d1.JPG)

After re-initializing DC shell, here's the magic! The library is already associated for us with relevant, link and target moniker.

![lab3_success_dcsetup](https://user-images.githubusercontent.com/73732594/135142218-7109d1d5-9298-4ac1-8c40-7cf19a63c47e.JPG)

## Tcl_scripting

Having seen basic steps for getting started with DC, for getting serious with it, it is important to know how to script in TCL.

A basic tutorial for setting the value of a variable, calling it out using echo and then using for loop in TCL is shown below. The point worth noting here is that while setting the value and using it in 'incr', we don't provide '$' in front of it.

![lab4_tcl1](https://user-images.githubusercontent.com/73732594/135143398-59fab214-2e50-4e6b-a9ce-4b3f1dcc4261.JPG)

For the same functionality using while, we have the following code snippet.

![lab4_tcl2](https://user-images.githubusercontent.com/73732594/135143519-e15a68e6-7b2c-4867-97b0-b0424c749e69.JPG)

Here the 'incr' function is expanded upon in the fashion i=i+1 using 'expr'.

![lab4_tcl3_incr_expr_equivalent](https://user-images.githubusercontent.com/73732594/135143668-176eb857-d1ef-4b9f-ab9a-db60ea74a541.JPG)

Initializing the list is done as follows using TCL.

![lab4_tcl4_list](https://user-images.githubusercontent.com/73732594/135143772-4a69311f-64a9-430d-a724-1ea3626289b1.JPG)


Illustrating the foreach command here.

![lab4_tcl5_foreach](https://user-images.githubusercontent.com/73732594/135144115-68f1523c-ec38-4fd5-b5ec-5539ed0826a5.JPG)

Here we have used a Synopsys command to get the standard cells containing 'and' in their reference name, the '*/*' wildcard ensures that we have any other text attached to it, so we have 'nand' here as well. The curly braces, signify that this is a Synopsys datatype called 'collection'.

![lab5_get_lib_cells](https://user-images.githubusercontent.com/73732594/135144606-eb26b6c8-c0f0-480f-b20a-66866edb81b3.JPG)

For accessing collection elements we have to make use of foreach_in_collection, but it is not as straighforward as foreach, as the variable yields a pointer like representation, and therefoe we have to dereference it using get_object_name and assign it to another varible.

![lab4_tcl6_pointer](https://user-images.githubusercontent.com/73732594/135144993-76a2d38e-850a-4e25-a253-dc18935b2e22.JPG)

![lab4_tcl6_getobjname](https://user-images.githubusercontent.com/73732594/135145014-8e300976-eb7d-4fd2-bc79-48791f62facf.JPG)

We can save the above as a .tcl file and source it in our shell environment to use it repeatedly.

![lab4_tcl6_muscript1](https://user-images.githubusercontent.com/73732594/135145230-c85b0c48-80f2-4434-aa09-057d138c23ae.JPG)

The command list_lib gives us the library loaded in tool library.

![lab7_listing_libraries](https://user-images.githubusercontent.com/73732594/135145348-dec7ec37-e281-479e-9651-7f64f7fede57.JPG)

Similarly,for getting the pins for the cells, we have to use get_lib_pins as shown below.

![lab7_lib_pins](https://user-images.githubusercontent.com/73732594/135146164-a90ba21e-3f43-4050-a16d-c810af82d9ef.JPG)

Here, we have written a script, which utilized get_lib_attribute to get the direction of the pins of the cells, and listing it out with the respective cell names, 1 specifying an input and 2 an output.

![Screenshot (336)](https://user-images.githubusercontent.com/73732594/135148960-01f57f96-17b9-4213-8fea-2df91c2ea801.png)

![lab7_pin_dir_script](https://user-images.githubusercontent.com/73732594/135146665-599a2169-b938-4352-915d-d0b8a32e50b3.JPG)

After that we used the function attribute to yield the functionality in boolean terms with respective cells.

![lab7_script](https://user-images.githubusercontent.com/73732594/135146788-effd609c-d622-4126-a546-1619ec499eee.JPG)

![lab7_scriptsuccess](https://user-images.githubusercontent.com/73732594/135146859-f9c6e9c0-61d1-4ca9-8875-9dd8232987e9.JPG)

Similarly, we can see the area (in um^2), by using the area attribute in following fashion.

![lab7_area_attribute](https://user-images.githubusercontent.com/73732594/135146991-18bedf44-aa97-4fdf-8e03-fa73ad9f60e8.JPG)

Some more attributes showcased here, pin capacitance, a port being a clock, and a cell being sequential showcased below:

![lab7_more_attributes](https://user-images.githubusercontent.com/73732594/135147186-9f373b21-29f5-486e-881c-d6749e3832f0.JPG)

We can list out the attributes and understand their use case as per our needs.

![lab7_list_attributes](https://user-images.githubusercontent.com/73732594/135147229-dafd07af-fa98-44bc-8425-dc4c1750ef69.JPG)

# Day-2

## STA_Basics

For carrying out STA based operations, we focus on the following design.

![Screenshot (340)](https://user-images.githubusercontent.com/73732594/135149018-0cabaf9a-1c0b-44d9-9cdb-91217f9fbf87.png)

![Screenshot (338)](https://user-images.githubusercontent.com/73732594/135149051-e32ec05d-7eaa-4b50-83cb-1be87672d2d5.png)

![lab8_circuit](https://user-images.githubusercontent.com/73732594/135149843-f814006e-62e3-4de5-8c79-a95e3e5465fe.JPG)

![Screenshot (260)](https://user-images.githubusercontent.com/73732594/135165314-2aaa91b6-8b43-4180-bd79-3edc443065ae.png)


We see the area and have a look at our design's ports here.

![lab8_aftercompile_ultra](https://user-images.githubusercontent.com/73732594/135150012-772ec9b9-f7a2-478c-9fc5-b1027cb81d51.JPG)

Since it is easier to access the collection objects after being de-referenced, here we have a script to list it out in an item by item basis.

![lab8_foreach_portname](https://user-images.githubusercontent.com/73732594/135150245-c1c99c8c-4c8d-493f-aded-dc8fbf468249.JPG)

Only port names, are not much useful, listing out their pin direction is pretty useful to get a general idea of our design.

![lab8_port_dir_script](https://user-images.githubusercontent.com/73732594/135150387-43514e65-fe5a-43f1-9340-10cdc1a9062f.JPG)

The following command helps us in identifying whether we have a flt design or a hierarchical one.

![lab8_hierarchical](https://user-images.githubusercontent.com/73732594/135150442-fcf8b254-fa6e-4835-997e-6b1c64661a6f.JPG)

Since, all of our cells aren't hierarchical in nature, we expectedly get the following on setting up a filter checking for non-hierarchical entries.

![lab8_all_physical](https://user-images.githubusercontent.com/73732594/135151242-54b4c102-56ae-496f-b74f-abbb90001467.JPG)

If we want to get the reference name for our instantiated unit, we can use the following attribute as shown. There's information in the name, df, signifies d-flip flop, r-asynchronous reset and t-true output, i.e. Q not Q', and p signifying positive edge is taken as clock-reference.

![lab8_reference_name](https://user-images.githubusercontent.com/73732594/135152150-11ba3efa-2af6-4dde-92dd-af5fcfd2fafa.JPG)

As a natural progression of our scripting prowess, we write a script yielding the cells with their reference names.

![lab8_reference_name_script](https://user-images.githubusercontent.com/73732594/135152749-b425a700-a441-4348-b649-a2191d33c377.JPG)

After reading in the verilog file and generating the ddc file, we take a look at the schematic on Design Vision.

![lab_8_ddc_schem](https://user-images.githubusercontent.com/73732594/135152905-f22044f8-973f-4c7b-9d32-b68225b44c8d.JPG)

It is a crime to have multiple drivers, and we can ensure over an net, that there be one input and multiple outputs, not in excess though as we will discuss later.

![lab8_script_driver](https://user-images.githubusercontent.com/73732594/135153251-b5100ec2-a3e4-49c9-b3f7-cb30767cd419.JPG)

Let us make our script yield all the devices with their pin names used in the design.

![lab9_pin_names](https://user-images.githubusercontent.com/73732594/135153492-07935259-17fd-4b70-8023-dd601fad8128.JPG)

## Constraints_and_IO_load

Having fun with the ports and seeing how we can access them as per our convenience. It's time we add physical signficance and deal with them.

![lab10_get_clocks](https://user-images.githubusercontent.com/73732594/135154312-56c361cc-3629-4f55-9a7f-84fee24bf194.JPG)

Profiling the clock, we see that it is a master clock and not a generated clock.

![lab10_masterclk_report](https://user-images.githubusercontent.com/73732594/135155224-9562a57c-4ff7-43f9-ab7a-74170e142111.JPG)

We can provide different waveform patterns and duty cycle as following.

![lab10_clock_different_wave](https://user-images.githubusercontent.com/73732594/135155421-e15dc2c5-ecdc-440f-bd2e-5c9288eeef1b.JPG)

![lab10_clock_varying_duty_cycle](https://user-images.githubusercontent.com/73732594/135155473-dfbc8d6f-2d55-45c0-a82c-399ce2b2e2b1.JPG)

We provide the source and network latency, along with the clock uncertainty as follows:

![lab10_source nw_latency](https://user-images.githubusercontent.com/73732594/135155500-0511c8dc-3aca-4a73-9c6f-c0fd33e72083.JPG)

![lab10_setup hold](https://user-images.githubusercontent.com/73732594/135155633-95d6ea40-d569-442e-b8c1-e5b0dd35bee7.JPG)

## Timing_and_Exploring_dot_lib_files

Unateness is discussed as per the timing arc characteristics the gate pins exhibit:

![Screenshot (334)](https://user-images.githubusercontent.com/73732594/135163949-5d82fc9a-064b-44b5-a83f-9a832fc5e88c.png)

Here, we see how the following gates differ in their driving strengths.

![Screenshot (328)](https://user-images.githubusercontent.com/73732594/135162366-106e4c5d-c62d-4db4-9bcd-565224d7ac6b.png)
![Screenshot (327)](https://user-images.githubusercontent.com/73732594/135162420-042f250c-0caa-4833-8cad-9172e6f532da.png)

The output capacitance is modelled as follows:

![Screenshot (325)](https://user-images.githubusercontent.com/73732594/135162662-b8542109-6ab0-4291-a1f4-cd701396b358.png)

The delay is a function of input transition and output load.

![Screenshot (326)](https://user-images.githubusercontent.com/73732594/135162581-4a1a236e-5029-46cc-b1ea-36e4fa162c93.png)

![Screenshot (344)](https://user-images.githubusercontent.com/73732594/135164160-a0671f11-91f5-425f-8b67-c9bccc6789af.png)

![Screenshot (345)](https://user-images.githubusercontent.com/73732594/135164206-dd2c7bee-ebc1-4f9b-adf3-923228911603.png)

![Screenshot (347)](https://user-images.githubusercontent.com/73732594/135164234-ae35337c-c31a-4d53-9af0-bea8295d27de.png)

![Screenshot (350)](https://user-images.githubusercontent.com/73732594/135164289-20f3a47a-3523-4627-a0f5-7e947a50e584.png)

Before providing any of the parameters as above we are reported an unconstrained design.

![lab10_unconstrained_timing](https://user-images.githubusercontent.com/73732594/135156190-f68a355d-161e-4f60-b12d-0fca832e2db7.JPG)

![lab10_regctod_unconstrained](https://user-images.githubusercontent.com/73732594/135156274-643f5003-39e2-414f-b0e8-a7876109ded2.JPG)

# Day-3

## Clock-tree_modelling

As we see the setup timing report, our timing is met as per the equation for setup slack.

![lab10_report_timing1](https://user-images.githubusercontent.com/73732594/135155973-7e79cadf-be7f-45ce-bbb7-2e0e0edfd202.JPG)

![lab10_constrained](https://user-images.githubusercontent.com/73732594/135156248-a1c8fc86-5dba-4b22-ad4d-4544581d52b3.JPG)

The verbose port report tells us there are a lot of gaps to be filled in the following.

## Clock-network_modelling_with_IO_delays

![lab12_input_delayverbose](https://user-images.githubusercontent.com/73732594/135156416-23b2ecad-42ce-4a03-a4ce-b1f71077624a.JPG)

After having taken care of setup constraints, we need to provide constraints for hold timing as well.

![lab12_hold_unconstrained](https://user-images.githubusercontent.com/73732594/135156756-af3ca803-26cd-4c75-8fe2-f340f1aaa25a.JPG)

After constraining it suitably, we have the following report for hold-timing.

![lab12_input_port_min_timing](https://user-images.githubusercontent.com/73732594/135156899-58aea3c5-b0a4-445d-8391-3addd75adbd5.JPG)

Having provided input transitions, we see the following with our slack minimizing.

![lab12_withinputtransition](https://user-images.githubusercontent.com/73732594/135157352-5c2bac86-b5d9-4cc4-826c-58be92b89d97.JPG)

After having dealt with input port timing, we head onto output, while constraining it suitably without the load first, and then with load afterwards.

![lab12_outputtiming_initial](https://user-images.githubusercontent.com/73732594/135157622-ad81bdfc-0fbc-457e-bcae-778d75926281.JPG)

![lab12_outputwithload](https://user-images.githubusercontent.com/73732594/135157642-43076115-dca1-4af4-8e14-178d86f4a28f.JPG)

For our output hold  timings, we make the changes as well, and it yields the following.

![lab12mindelay_out](https://user-images.githubusercontent.com/73732594/135157935-c6b99ed6-7982-41cf-915f-96ee08b57f70.JPG)

![Screenshot (256)](https://user-images.githubusercontent.com/73732594/135165537-13101944-a5ab-4955-9057-0b423074a2ad.png)

![Screenshot (254)](https://user-images.githubusercontent.com/73732594/135165608-49399cb0-6b3f-49a4-a2d3-b0ae53805bea.png)

![Screenshot (253)](https://user-images.githubusercontent.com/73732594/135165639-95692b14-5577-46e2-8c7b-eced6cfc8fa9.png)

![Screenshot (252)](https://user-images.githubusercontent.com/73732594/135165710-c3a11e3c-eafb-482f-9fc5-5f20ac6892e6.png)

![Screenshot (245)](https://user-images.githubusercontent.com/73732594/135165778-42c96e12-39b1-46f9-b995-38cb5fde4050.png)

![Screenshot (243)](https://user-images.githubusercontent.com/73732594/135165828-92e5be27-a2a2-4fb1-9748-6b8e80a8552d.png)

# Day-4

## Combinational_and_resource_sharing_optimization

There is Boolean Optimization, on an algebraic level, as it simplifies down the line following the basic laws of Boolean Algebra, while resource sharing is a bit involved as it focusses on the constraint being provided, and therefore tries to optimize the design structure in a manner where if possible it will strive for minimum area and where not possible it will focus on other metrics like the overall delay.

![Screenshot (271)](https://user-images.githubusercontent.com/73732594/135165032-5d0e452c-be83-42c1-a26c-d6d1ba0ca7fc.png)

![Screenshot (272)](https://user-images.githubusercontent.com/73732594/135164982-a1ef2a05-29fe-4f80-a69a-0423b3bb3958.png)

## Sequential_optimization

Constant propagation is a method which basically ties down one of the inputs to either high or low voltage specified for the design, and as we see they utilize tie-cells which are further made of resistive implementation of the CMOS transistors connected in the following fashion. The retiming paradigm is a tricky one, in case you had combinational logic provided as a whole which realized a whole logic in such a manner that made sense on having a look at the logic diagram, if spreading out the logic soa s to yield a functional equivalent for timing optimization, is not problematic, one may opt for it. The Constant propagation method of simplification may also lead to a combinational logic block like an inverter, as per the implementation specified.

![Screenshot (267)](https://user-images.githubusercontent.com/73732594/135165160-d789b285-43c1-4bc0-b879-9391e6a00b92.png)

![Screenshot (286)](https://user-images.githubusercontent.com/73732594/135164572-2031dd67-5c3a-4e88-8348-430e8b4b2945.png)

![Screenshot (284)](https://user-images.githubusercontent.com/73732594/135164609-bd21269a-fc48-42ed-ad89-b325708b3f0e.png)

![Screenshot (276)](https://user-images.githubusercontent.com/73732594/135164882-df1f5b93-e312-405a-a904-05b0ae7de7a6.png)

![Screenshot (275)](https://user-images.githubusercontent.com/73732594/135164905-8d3de889-a546-42e0-9d3f-2817bc248b76.png)

![Screenshot (270)](https://user-images.githubusercontent.com/73732594/135165092-c7b1ae1e-19b1-43b8-a868-a67d1b1068f0.png)

![Screenshot (269)](https://user-images.githubusercontent.com/73732594/135165119-4a9bfe2d-1839-4581-999c-d3fe75d2ef58.png)



## Special_optimization

In the special optimization methods, we provide special commands to DC which basically provides it instruction to provide boundaries where possible by not making a melting pot of combinational logic where explicit boundaries aren't specified, so that we can basically provide better observability and controllability. The multicycle paths are dealt with explicitly specifying which path is better off considering an extra clock cycle, rather than optimizing it for a single cycle, which may lead to catastrophic results. Also, we try to isolate our output port by introducing buffer which handles the output capacitance at the boundary rather than our pin which might get loaded to a pretty high value, and so as to incorporate constraints may get lost in the optimization process. 

![Screenshot (293)](https://user-images.githubusercontent.com/73732594/135163186-bcfc42b4-cdd4-41c7-aed5-a53b1539508a.png)

![Screenshot (286)](https://user-images.githubusercontent.com/73732594/135163654-3351747c-20b5-4177-a672-e03b62bb257c.png)

![Screenshot (287)](https://user-images.githubusercontent.com/73732594/135164533-d75d9d3c-36ec-4352-a7cd-dc1535cf39d9.png)

![Screenshot (280)](https://user-images.githubusercontent.com/73732594/135164710-ba26ef90-6b1f-417f-aef4-2da14d931a5d.png)

![Screenshot (279)](https://user-images.githubusercontent.com/73732594/135164754-3bf540b7-1f9e-4d10-8dea-9e307723f6ac.png)



# Day-5

## Timing_report

The timing reports are taken with consideration to various max and min transitions as well as the combinational delay provided along with output, and the worst paths ay be segregated and accessed with special commands, to yield various paths, but as per our setup-path timing, we consider max's and min's in case of hold timing analysis. 

![Screenshot (299)](https://user-images.githubusercontent.com/73732594/135163031-01582bba-acab-4d05-abd8-432122720324.png)

![Screenshot (298)](https://user-images.githubusercontent.com/73732594/135163074-2480f7df-fb8a-4cc5-9408-7c9038997a59.png)


## Set_max_capacitance_HFN

When we have a High fanout network, it is important for us to structure it in a way that buffers are inserted in a tree-like fashion between various nodes yielding a load distribution which is decided upon by the max capacitance, the node sees, and we have specified as a constraint.

## Summary

In summary, logical synthesis is a combination of logic translation, mapping and optimization, which require us to use the brilliant tool with a matching brilliance on each step, taking care of the following. And for ensuring a healthy slack, we have to make meaningful assignments to our constraints, so that the compilation tool, can act on it and provide us with the magical reality of having the slack met !

![Screenshot (304)](https://user-images.githubusercontent.com/73732594/135162921-14da100c-27c4-4989-9574-e30cb9bf3923.png)
![Screenshot (306)](https://user-images.githubusercontent.com/73732594/135163806-8c24a0a9-1d65-4819-8dc0-6147a8ae4843.png)
![Screenshot (303)](https://user-images.githubusercontent.com/73732594/135163728-b767805d-8e3c-41ef-bef1-0fc8ec1420fa.png)


## Acknowledgements:

* [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.

## References:

**Websites**

* VSD-IAT: https://vsdiat.com/


