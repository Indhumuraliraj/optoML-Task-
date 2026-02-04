# optoML-Task
Implementation of a single-stage pipeline register in SystemVerilog using a standard valid/ready handshake. The module stores input data when in_valid and in_ready are asserted, presents stored data at the output with out_valid, and handles backpressure correctly without data loss or duplication. Fully synthesizable and resettable to an empty state

# Single-Stage Pipeline Register (SystemVerilog)

## Problem Description

The task is to design a pipeline register that sits between an input interface and an output interface:

Input: data (8-bit), in_valid, in_ready,out_valid

Output: data_out (8-bit)

Clock: clk

Reset: rst

## Functional Requirements:

Accept input data only when both in_valid and in_ready are high.

Store accepted data internally.

Present stored data at the output with out_valid asserted.

> Handle backpressure: if the output is not ready, hold the data until it can be accepted.

> Data Loss: Input data is never stored or gets overwritten before it reaches the output.

> Data Duplication: The same data is sent to the output more than once.

 Fully synthesizable and resettable to a clean empty state.

## Program

## Design file(register.sv)

```
module register(clk,rst,in_valid,in_ready,out_valid,data,data_out);
  input logic clk,rst,in_valid,in_ready,out_valid;
  input logic [7:0] data;
  output logic [7:0] data_out;
  
  reg [7:0]mem;
  
  always @(posedge clk or negedge clk)
    begin
      if(rst)
        data_out <= 8'b0;
      else if(in_valid && in_ready)
        mem <= data;
      else if(out_valid)
        data_out <= mem;
      else
        data_out <= 8'b0;
    end
  
  initial begin
    $dumpfile("dump.vcd"); 
    $dumpvars;
  end
          
endmodule
```

## Testbench (register_tb.sv)

```
`timescale 1ns/1ps

module register_tb;

  logic clk;
  logic rst;
  logic in_valid;
  logic in_ready;
  logic out_valid;
  logic [7:0] data;
  logic [7:0] data_out;

  register dut (
    .clk(clk),
    .rst(rst),
    .in_valid(in_valid),
    .in_ready(in_ready),
    .out_valid(out_valid),
    .data(data),
    .data_out(data_out)
  );

  initial clk = 0;
  always #5 clk = ~clk;


  initial begin
    rst = 1;
    in_valid = 0;
    in_ready = 0;
    data = 8'b0;

    #10;
    rst = 0;
    data = 8'b10101010;
    in_valid = 1;
    in_ready = 1;
    out_valid = 1;
    
    #10;
   
    in_valid = 0;
    in_ready = 0;
    
    #10;
    rst = 0;
    data = 8'b10111110;
    in_valid = 1;
    in_ready = 1;
    out_valid = 1;
   
    #10;
    in_valid = 0;
    in_ready = 0;
    
    #20;
    $finish;
  end

endmodule

```

## To get a waveform

```

  initial begin
    $dumpfile("dump.vcd"); 
    $dumpvars;
  end
  
```

## Output

![image Alt]


