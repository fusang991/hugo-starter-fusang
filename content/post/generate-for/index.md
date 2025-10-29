---
title: "Generate For vs. For"
description: 
date: 2025-10-29T15:50:08+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
    - verilog
---


# generate for 

## ex1
``` verilog

module generate_for1(
input logic clk,
input  logic in,
output logic [3:0]out
    );
    
genvar i;   

generate 
for(i=1;i<=3;i=i+1) begin
always_ff@(posedge clk) begin
out[0]<=in;

out[i]<=out[i-1];
end
end
endgenerate
endmodule
```
![generate1](generate1.png)

## ex2
```verilog
module generate_for1(
input logic clk,
input  logic in,
output logic [3:0]out
    );
    
genvar i;   

generate 
for(i=0;i<=3;i=i+1) begin
always_ff@(posedge clk) begin
//out[0]<=in;

out[i]<=out[i-1];
end
end
endgenerate
endmodule
```
![generate2](generate2.png)

# for 
## ex1
``` verilog
module for1(
input logic clk,
input  logic in,
output logic [3:0]out
    );
    
integer i;   
always_ff@(posedge clk) begin
out[0]<=in;
for(i=1;i<=3;i=i+1) begin 
out[i]<=out[i-1];
end

end 
endmodule
```
![for1](2.png)

## ex2

```verilog
module for1(
input logic clk,
input  logic in,
output logic [3:0]out
    );
    
integer i;   
always_ff@(posedge clk) begin
out[0]<=in;
for(i=0;i<=3;i=i+1) begin 
out[i]<=out[i-1];
end

end 
endmodule
```

![for2](for2.png)