# Vending_Machine
# EXP NO: 6.B  Design and simulate a Vending Machine Controller using Verilog HDL, and verify its functionality using a testbench
# Aim
To design and simulate a Vending Machine Controller using Verilog HDL, and verify its functionality using a testbench in Vivado.

# Apparatus required:
Vivad0 

# Problem Statement
Design a vending machine that:
Accepts ₹5 and ₹10 coins
Item cost = ₹15
Dispenses product when ₹15 or more is inserted
Returns change if amount exceeds ₹15

# State Diagram (Concept)
States: S0 → 0 Rs
        S5 → 5 Rs
        S10 → 10 Rs
        S15 → Dispense

# Verilog Code (Moore FSM)
```
`timescale 1ns / 1ps
module vending_machine(
     input clk,
     input reset,
     input coin5,
     input coin10,
     output reg product,
     output reg change
);

parameter S0  = 3'b000,
           S5  = 3'b001,
           S10 = 3'b010,
           S15 = 3'b011,
           S20 = 3'b100;

reg [2:0] state, next_state;

always @(posedge clk) begin
     if(reset)
         state <= S0;
     else
         state <= next_state;
end

always @(*) begin
     next_state = state;

     case(state)
         S0:  begin
             if(coin5)       next_state = S5;
             else if(coin10) next_state = S10;
         end

         S5:  begin
             if(coin5)       next_state = S10;
             else if(coin10) next_state = S15;
         end

         S10: begin
             if(coin5)       next_state = S15;
             else if(coin10) next_state = S20;
         end

         S15: next_state = S0;

         S20: next_state = S0;

         default: next_state = S0;
     endcase
end

always @(posedge clk) begin
     if(reset) begin
         product <= 0;
         change  <= 0;
     end
     else begin
         product <= (next_state == S15 || next_state == S20);
         change  <= (next_state == S20);
     end
end

endmodule
```
# Testbench
```

`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
// Company: 
// Engineer: 
// 
// Create Date: 06.08.2026 17:08:05
// Design Name: 
// Module Name: tb_vendor
// Project Name: 
// Target Devices: 
// Tool Versions: 
// Description: 
// 
// Dependencies: 
// 
// Revision:
// Revision 0.01 - File Created
// Additional Comments:
// 
//////////////////////////////////////////////////////////////////////////////////




module tb_vending_machine;

reg clk;
reg reset;
reg coin5;
reg coin10;

wire product;
wire change;

// Instantiate the vending machine
vending_machine uut (
    .clk(clk),
    .reset(reset),
    .coin5(coin5),
    .coin10(coin10),
    .product(product),
    .change(change)
);

// Clock generation (10ns period)
always #5 clk = ~clk;

initial begin
    // Initialize inputs
    clk = 0;
    reset = 1;
    coin5 = 0;
    coin10 = 0;

    // Apply reset
    #10 reset = 0;

    // Test Case 1: 5 + 10 = 15 (product)
    #10 coin5 = 1;  #10 coin5 = 0;
    #10 coin10 = 1; #10 coin10 = 0;

    // Wait
    #20;

    // Test Case 2: 10 + 10 = 20 (product + change)
    #10 coin10 = 1; #10 coin10 = 0;
    #10 coin10 = 1; #10 coin10 = 0;

    // Wait
    #20;

    // Test Case 3: 5 + 5 + 5 = 15 (product)
    #10 coin5 = 1; #10 coin5 = 0;
    #10 coin5 = 1; #10 coin5 = 0;
    #10 coin5 = 1; #10 coin5 = 0;

    // Wait
    #30;

    $finish;
end

// Monitor signals
initial begin
    $monitor("Time=%0t | coin5=%b coin10=%b | product=%b change=%b",
              $time, coin5, coin10, product, change);
end

endmodule


```
Expected Waveform Behavior
Case 1: 5 + 5 + 5
   dispense = 1
   change = 0
Case 2: 10 + 5
   dispense = 1
   change = 0
Case 3: 10 + 10
   dispense = 1
   change = 1

# Output waveform 
<img width="1592" height="892" alt="image" src="https://github.com/user-attachments/assets/d08390ce-2af0-499d-b3e1-cec66686b6dd" />

<img width="900" height="1600" alt="WhatsApp Image 2026-08-07 at 1 55 02 PM" src="https://github.com/user-attachments/assets/34ef8fc4-554d-4b8e-b8a5-d8ccb8189e5a" />



# Conclusion
The vending machine controller was successfully designed using a Moore FSM model. The simulation verified correct product dispensing and change return behavior for different coin inputs.
