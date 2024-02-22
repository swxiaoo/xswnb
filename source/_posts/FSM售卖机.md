---
title: FSM售卖机
date: 2024-02-21 17:48:23
tags:
---

# Verilog中的自动售卖机FSM设计教程

在这篇教程中，我们将通过设计一个简单的自动售卖机来学习如何使用Verilog语言实现有限状态机（FSM）。自动售卖机将接受两种硬币：5元和10元，以售卖一个15元的商品为例。

## 设计目标

我们的目标是设计一个FSM，它可以：

- 接受5元和10元两种输入
- 当累计金额达到商品价格15元时，释放商品
- 如果投入超过商品价格，计算并返回找零

## 设计步骤

### 1. 定义状态

首先，我们需要定义FSM的所有可能状态。对于自动售卖机，我们可以设定以下状态：

- `S0`：初始状态（无钱）
- `S5`：已接收5元
- `S10`：已接收10元
- `S15`：已累计15元，准备释放商品并返回初始状态

### 2. 定义输入

我们的自动售卖机接受两种输入：

- `coin5`：投入5元
- `coin10`：投入10元

### 3. 状态转移和输出逻辑

接下来，我们需要定义在各种输入下，从一个状态到另一个状态的转移，以及相应的输出行为。

### 4. Verilog代码实现

#### 定义模块和端口

```verilog
module VendingMachineFSM(
    input wire clk,
    input wire reset,
    input wire coin5,
    input wire coin10,
    output reg release,
    output reg [1:0] change
);

//状态和参数定义
reg [1:0] state;
reg [1:0] next_state;

parameter S0 = 2'b00, S5 = 2'b01, S10 = 2'b10, S15 = 2'b11;

//状态转移逻辑
always @(posedge clk or posedge reset) begin
    if (reset) state <= S0;
    else state <= next_state;
end

//下一个状态和输出的决定

always @(*) begin
    case (state)
        S0: begin
            if (coin5) next_state = S5;
            else if (coin10) next_state = S10;
            else next_state = S0;
        end
        S5: begin
            if (coin5) next_state = S10;
            else if (coin10) next_state = S15;
            else next_state = S5;
        end
        S10: begin
            if (coin5) next_state = S15;
            else next_state = S10;
        end
        S15: begin
            next_state = S0; // 商品释放后返回初始状态
        end
        default: next_state = S0;
    endcase
end

//控制输出
always @(state) begin
    case (state)
        S15: begin
            release = 1'b1;
            change = 0; // 假设顾客投入金额恰好
        end
        default: begin
            release = 1'b0;
            change = 0; // 无需找零
        end
    endcase
end
endmodule
```

## 总结

通过本教程，我们学习了如何使用Verilog设计一个简单的自动售卖机FSM。这个例子展示了FSM的基本概念，状态定义，以及状态转移逻辑的实现方法。希望这能帮助你在使用Verilog进行数字设计时有一个好的开始。 