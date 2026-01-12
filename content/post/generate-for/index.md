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
    - other
tags:
    - wip
    - verilog
---


## generate for

### generate for 1

```verilog
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

### generate for2

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

> 草率了，忘记数组不能负索引了，但是负索引竟然可以综合

### generate for3

```verilog
module generate_for1(
input logic clk,
input  logic in,
output logic [3:0]out
    );

genvar i;   

generate 
for(i=1;i<=3;i=i+1) begin
always_ff@(posedge clk) begin
//out[0]<=in;
out[i]<=out[i-1];
end
always_ff@(posedge clk) begin
out[0]<=in;
end


end
endgenerate
endmodule
```

![generate3](generate3.png)

### generate for 4

```verilog
module generate_for1(
input logic clk,
input  logic in,
output logic [3:0]out
    );

genvar i;   

generate 
for(i=1;i<=3;i=i+1) begin
always_ff@(posedge clk) begin
//out[0]<=in;
out[i]<=out[i-1];
end
end
endgenerate
always_ff@(posedge clk) begin
out[0]<=in;
end
endmodule
```

![generate4](generate4.png)

## for

### for1

```verilog
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

### for2

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

> 草率了，忘记数组不能负索引了，但是负索引竟然可以综合
