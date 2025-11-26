# Energy-Efficient-Intelligent-Streetlight-with-Dynamic-Brightness-Control
# Project Objective

This project aims to design and implement an Adaptive Smart Streetlight Controller using Verilog HDL, capable of automatically adjusting its brightness in response to real-time conditions. The system is designed to:

Turn OFF during daytime.

Maintain DIM illumination at night when no vehicle is present.

Switch to BRIGHT illumination when a vehicle is detected at night.

Display the lighting state using RGB LED output, with each color representing a specific mode.

# Key Functional Units
(1) Sensor Input Module — day, veh_detect

day simulates ambient light sensing (day/night condition).

veh_detect simulates vehicle or motion detection.

Both inputs are implemented using on-board switches

# RGB Output Module

Instead of physical dimming hardware, an RGB LED visually represents illumination levels:

Output	Meaning
Red (R)=1 → OFF state	
Green (G)=1 → DIM state	
Blue (B)=1 → BRIGHT state

# FSM (Finite State Machine) Controller

A 3-state FSM governs the light-control logic:

| FSM State | Meaning                  | Lighting Mode        |
| --------- | ------------------------ | -------------------- |
| `OFF`     | Daytime                  | Streetlight OFF      |
| `DIM`     | Night – No vehicle       | Low illumination     |
| `BRIGHT`  | Night – Vehicle detected | Maximum illumination |

# Clock / Timing Module

The FSM operates synchronously based on the positive clock edge, ensuring deterministic state transitions.

# Functional Operation

| Condition                      | System State | RGB Output | Light Intensity |
| ------------------------------ | ------------ | ---------- | --------------- |
| `day = 1`                      | OFF          | R = 1      | Light OFF       |
| `day = 0` and `veh_detect = 0` | DIM          | G = 1      | Low brightness  |
| `day = 0` and `veh_detect = 1` | BRIGHT       | B = 1      | Full brightness |

# State Transition Summary

Daytime (day = 1) → Always OFF

Night (day = 0) + No Vehicle → DIM

Night (day = 0) + Vehicle Detected → BRIGHT

# VERILOG Code

```
`timescale 1ns / 1ps
module street_light_project(
    input clk,
    input rst,
    input day,
    input veh_detect,
    output reg R,
    output reg G,
    output reg B
);

    parameter off = 2'b00;
    parameter dim = 2'b01;
    parameter bright = 2'b10;

    reg [1:0] current_state, next_state;

    always @(posedge clk or posedge rst) begin
        if (rst)
            current_state <= off;
        else
            current_state <= next_state;
    end

    always @(*) begin
        case (current_state)
            off:    next_state = day ? off : dim;
            dim:    next_state = day ? off : (veh_detect ? bright : dim);
            bright: next_state = day ? off : (!veh_detect ? dim : bright);
            default: next_state = off;
        endcase
    end

    always @(*) begin
        case (current_state)
            off:    begin R = 1; G = 0; B = 0; end
            dim:    begin R = 0; G = 1; B = 0; end
            bright: begin R = 0; G = 0; B = 1; end
            default: begin R = 0; G = 0; B = 0; end
        endcase
    end

endmodule
```
# Constraint File

```
set_property -dict {PACKAGE_PIN F14 IOSTANDARD LVCMOS33} [get_ports {clk}]
set_property -dict {PACKAGE_PIN U1 IOSTANDARD LVCMOS33} [get_ports {rst}]
set_property -dict {PACKAGE_PIN V2 IOSTANDARD LVCMOS33} [get_ports {day}]
set_property -dict {PACKAGE_PIN U2 IOSTANDARD LVCMOS33} [get_ports {veh_detect}]

set_property -dict {PACKAGE_PIN V6 IOSTANDARD LVCMOS33} [get_ports {R}]
set_property -dict {PACKAGE_PIN V4 IOSTANDARD LVCMOS33} [get_ports {G}]
set_property -dict {PACKAGE_PIN U6 IOSTANDARD LVCMOS33} [get_ports {B}]

```

# OUTPUT 

![WhatsApp Image 2025-11-26 at 11 21 45_4c25aa49](https://github.com/user-attachments/assets/b714babf-b7af-4164-a8c1-0ed5957cf9c8)

![WhatsApp Image 2025-11-26 at 11 21 44_0def71b7](https://github.com/user-attachments/assets/44df3477-213a-4a71-ba02-ad0db8c98b16)

![WhatsApp Image 2025-11-26 at 11 21 43_5568ee9e](https://github.com/user-attachments/assets/716af6cb-730b-41cb-9b20-27993c46c01c)

![WhatsApp Image 2025-11-26 at 11 21 43_46df3f3f](https://github.com/user-attachments/assets/99bbc039-98e6-43bd-81b1-088a1c421a01)

# RESULT

The implemented controller results in a fully automatic street-lighting solution with zero manual intervention.
It behaves exactly like a real-world smart streetlight: conserving energy while ensuring safety, using a clean digital design based on FSM logic.
