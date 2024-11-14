SIMULATION AND IMPLEMENTATION OF LOGIC GATES
AIM:
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

APPARATUS REQUIRED:
Vivado 2023.1

Procedure
1. Launch Vivado
Open Vivado 2023.1 by double-clicking the Vivado icon or searching for it in the Start menu.
2. Create a New Project
Click on "Create Project" from the Vivado Quick Start window.
In the New Project Wizard:
Project Name: Enter a name for the project (e.g., Mux4_to_1).
Project Location: Select the folder where the project will be saved.
Click Next.
Project Type: Select RTL Project, then click Next.
Add Sources:
Click on "Add Files" to add the Verilog files (e.g., mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.).
Make sure to check the box "Copy sources into project" to avoid any external file dependencies.
Click Next.
Add Constraints: Skip this step by clicking Next (since no constraints are needed for simulation).
Default Part Selection:
You can choose a part based on the FPGA board you are using (if any).
If no board is used, you can choose any part, for example, xc7a35ticsg324-1L (Artix-7).
Click Next, then Finish.
3. Add Verilog Source Files
In the "Sources" window, right-click on "Design Sources" and select Add Sources if you didn't add all files earlier.
Add the Verilog files (mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.) and the testbench (mux4_to_1_tb.v).
4. Check Syntax
Expand the "Flow Navigator" on the left side of the Vivado interface.
Under "Synthesis", click "Run Synthesis".
Vivado will check your design for syntax errors. If any errors or warnings appear, correct them in the respective Verilog files and re-run the synthesis.
5. Simulate the Design
In the Flow Navigator, under "Simulation", click on "Run Simulation" → "Run Behavioral Simulation".
Vivado will open the Simulations Window, and the waveform window will show the signals defined in the testbench.
6. View and Analyze Simulation Results
The simulation waveform window will display the signals (S1, S0, A, B, C, D, Y_gate, Y_dataflow, Y_behavioral, Y_structural).
Use the time markers to verify the correctness of the 4:1 MUX output for each set of inputs.
You can zoom in/out or scroll through the simulation time using the waveform viewer controls.
7. Adjust Simulation Time
To run a longer simulation or adjust timing, go to the Simulation Settings by clicking "Simulation" → "Simulation Settings".
Under "Simulation", modify the Run Time (e.g., set to 1000ns).
8. Generate Simulation Report
Once the simulation is complete, you can generate a simulation report by right-clicking on the simulation results window and selecting "Export Simulation Results".
Save the report for reference in your lab records.
9. Save and Document Results
Save your project by clicking File → Save Project.
Take screenshots of the waveform window and include them in your lab report to document your results.
You can include the timing diagram from the simulation window showing the correct functionality of the 4:1 MUX across different select inputs and data inputs.
10. Close the Simulation
Once done, close the simulation by going to Simulation → "Close Simulation".

Logic Diagram

![image](https://github.com/user-attachments/assets/d4ab4bc3-12b0-44dc-8edb-9d586d8ba856)

Truth Table

![image](https://github.com/user-attachments/assets/c850506c-3f6e-4d6b-8574-939a914b2a5f)

Verilog Code
```
4:1 MUX Gate-Level Implementation

module mux_4to1 (
    input wire [1:0] sel, 
    input wire a, b, c, d,   
    output wire out          
);

    assign out = (sel == 2'b00) ? a :
                 (sel == 2'b01) ? b :
                 (sel == 2'b10) ? c :
                                  d;
endmodule
```
![Screenshot 2024-09-19 142021](https://github.com/user-attachments/assets/3ee86216-c93c-4ced-a7bb-32d2af9992b5)
   

4:1 MUX Data Flow Implementation
```
// mux4_to_1_dataflow.v
module mux4_to_1_dataflow (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,    
    output wire y
);
    assign Y = (~S1 & ~S0 & A) |
               (~S1 & S0 & B) |
               (S1 & ~S0 & C) |
               (S1 & S0 & D);

endmodule 
```

![Screenshot 2024-09-19 142717](https://github.com/user-attachments/assets/dd5789e4-feee-4029-aa34-50f9f0a9aae7)

4:1 MUX Behavioral Implementation
```
// mux4_to_1_behavioral.v
module mux4_to_1_behavioral (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output reg Y
);
    always @(*) begin
        case ({S1, S0})
            2'b00: Y = A;
            2'b01: Y = B;
            2'b10: Y = C;
            2'b11: Y = D;
            default: Y = 1'bx; // Undefined
        endcase
    end
endmodule
```
4:1 MUX Structural Implementation
```
module mux2_to_1 (a,s,out);
input s,[1:0]a;
output out;
    assign out = s ? a[1] : a[0];
endmodule
module mux4_to_1_structural (a,s,out);
input [3:0]a;
input [1:0]s;
output out;
    wire mux_low, mux_high;
    mux2_to_1 mux0 (.a[0](a[0]), .a[1](a[1]), .s(s[0]), .out(mux_low));
    mux2_to_1 mux1 (.a[0](a[2]), .a[1](a[3]), .s(s[0]), .out(mux_high));
    mux2_to_1 mux_final (.a[0](mux_low), .a[1](mux_high), .s(s[1]), .out(out));
endmodule
```
OUTPUT: ![image](https://github.com/user-attachments/assets/3dd64fd0-0e6e-41bc-be72-6e715faebb3b)


Testbench Implementation
```
`timescale 1ns / 1ps
module mux4_to_1_tb;
reg [3:0]a;
reg [1:0]s;
wire out;
    wire out_gate;
    wire out_dataflow;
    wire out_behavioral;
    wire out_structural;
    mux4_to_1_gate uut_gate (
        .a[0](a[0]),
        .a[1](a[1]),
        .a[2](a[2]),
        .a[3](a[3]),
        .s[0](s[0]),
        .s[1](s[1]),
        .out(out_gate)
    );
    mux4_to_1_dataflow uut_dataflow (
        .a[0](a[0]),
        .a[1](a[1]),
        .a[2](a[2]),
        .a[3](a[3]),
        .s[0](s[0]),
        .s[1](s[1]),
        .out(out_dataflow)
    );
    mux_4_1_behavioral uut_behavioral (
        .a[0](a[0]),
        .a[1](a[1]),
        .a[2](a[2]),
        .a[3](a[3]),
        .s[0](s[0]),
        .s[1](s[1]),
        .out(out_behavioral)
    );
    mux4_to_1_structural uut_structural (
        .a[0](a[0]),
        .a[1](a[1]),
        .a[2](a[2]),
        .a[3](a[3]),
        .s[0](s[0]),
        .s[1](s[1]),
        .out(out_structural)
    );
    initial begin
            a[0] = 0; a[1] = 0; a[2] = 0; a[3] = 0; s[0] = 0; s[1] = 0;
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b00_0000; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b00_0001; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b01_0010; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b10_0100; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b11_1000; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b01_1100; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b10_1010; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b11_0110; 
        #2 {s[1], s[0], a[0], a[1], a[2], a[3]} = 6'b00_1111; 
        #2 $stop;
    end
    initial begin
        $monitor("Time=%0t | s[1]=%b s[0]=%b | Inputs: a[0]=%b a[1]=%b a[2]=%b a[3]=%b | out_gate=%b | out_dataflow=%b | out_behavioral=%b | out_structural=%b",$time, s[1], s[0], a[0], a[1], a[2], a[3], out_gate, out_dataflow, out_behavioral, out_structural);
    end
endmodule
```
OUTPUT: ![image](https://github.com/user-attachments/assets/f82773df-dc8c-4791-8432-7383c6f3480a)


Sample Output

Time=0 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=10 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=20 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=30 | S1=0 S0=1 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=40 | S1=1 S0=0 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
...

Conclusion:

In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural. The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.



  
