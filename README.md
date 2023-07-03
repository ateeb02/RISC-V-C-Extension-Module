# *RISC-V C-Extension Module*
##### Author: Ateeb Tahir

#
## *Introduction To Compressed Extensions*

The `Compresed Extension`, also known as `C-Extension` is a feature of the RISC-V architecture that introduces a compressed instruction format. It allows instructions to be encoded in 16-bit formats instead of the usual 32-bits. It does so by representing commonly used instructions in a more compact form. This compression significantly reduces code size, which is particularly beneficial in memory-constrained environments or when executing code from slow memory.

The C-Extension defines a new set of compressed instructions that operate on a subset of the register file. These instructions therefore provide a subset of the functionality available in the base RISC-V instruction set. However, they can still perform essential operations such as arithmetic, logical, load/store, and control flow operations.

The Compressed Extension is fully compatible with the base RISC-V architecture, allowing systems to seamlessly support both compressed and uncompressed instructions. Processors that support the Compressed Extension can execute both types of instructions, providing flexibility in code optimization.

By leveraging the Compressed Extension, developers can reduce the memory footprint of their programs, leading to benefits such as reduced storage requirements, improved cache utilization, and potentially faster instruction fetches. However, it's important to note that not all instructions can be compressed, and there are limitations to the compression scheme. Certain instructions, such as those with larger immediate values or those involving complex operations, may not be eligible for compression.

## *C_Extension_Unit*

The `c_extension_unit` module is a Verilog module that implements the compressed extension. It has two submodules `c_misalign` and `c_decode`. The module supports all of the standard compressed instructions. The complete list of them is given below:

**R-Type Instructions:**

- ADD: `ADD rd, rs1, rs2`
- AND: `AND rd, rs1, rs2`
- OR: `OR rd, rs1, rs2`
- XOR: `XOR rd, rs1, rs2`
- SUB: `SUB rd, rs1, rs2`
- MV: `MV rd, rs2`
- SRLI: `SRLI rd, rs1, shamt`
- SRAI: `SRAI rd, rs1, shamt`

**I-Type Instructions:**

- ADDI: `ADDI rd, rs1, imm`
- LW: `LW rd, imm(rs1)`
- JALR: `JALR x0, rs1, 0`
- JALR: `JALR x1, rs1, 0`
- ADDI: `ADDI rd, x2, imm`
- ANDI: `ANDI rd, rs1, imm`
- SLLI: `SLLI rd, rs1, shamt`
- LI: `ADDI rd, x0, imm`

**S-Type Instructions:**

- SW: `SW rs2, imm(rs1)`
- SW: `SW rs2, imm(x2)`

**B-Type Instructions:**

- BEQ: `BEQ rs1', x0, offset`
- BNE: `BNE rs1', x0, offset`

**U-Type Instructions:**

- LUI: `LUI rd, imm`

**J-Type Instructions:**

- JAL: `JAL x1, offset`
- JAL: `JAL x0, offset`


### Inputs

- `clk_i`:          The clock signal for the module.
- `reset_i`:        The reset signal for the module.
- `pc_i`:           The 32-bit program counter input.
- `instr_i`:        The 32-bit instruction input.
- `br_taken_i`:     The branch taken signal input.

### Outputs

- `pc_realigned_o`: The realigned program counter output (32 bits).
- `pc_half_o`:      The half bit of the program counter output.
- `stall_o`:        The stall signal output.
- `instr_o`:        The compressed instruction output (32 bits).

### Internal Signals

- `pc_misalign`:    A signal to indicate if the program counter is misaligned.
- `instruction`:    A 32-bit signal to hold the instruction.

## _Instantiated Modules_

### `c_misalign`

The `c_misalign` module is instantiated to handle misalignment of the program counter and instruction. It takes the clock and reset signals, branch taken signal, program counter input (`pc_i`), and instruction input (`instr_i`). It provides the stall signal output (`stall_o`), program counter misalignment signal output (`pc_misalign`), realigned program counter output (`pc_realigned_o`), and instruction output (`instruction`).

### `c_decode`

The `c_decode` module is instantiated to decode the compressed instruction. It takes the instruction input (`instruction`), the half bit of the program counter (`pc_i[1]`), and the program counter misalignment signal input (`pc_misalign`). It provides the next_comp16 signal output (`next_comp16`) and the compressed instruction output (`instr_o`).

## Usage

To use the `c_extension_unit` module, instantiate it in your Verilog design and connect the necessary inputs and outputs. Provide the clock signal (`clk_i`), reset signal (`reset_i`), program counter input (`pc_i`), instruction input (`instr_i`), and branch taken signal input (`br_taken_i`). Connect the outputs as needed.

*Example:*

```systemverilog
module top_module (
    // Inputs
    input logic clk,
    input logic reset,
    input logic [31:0] pc,
    input logic [31:0] instr,
    input logic br_taken,

    // Outputs
    output logic [31:0] pc_realigned,
    output logic pc_half,
    output logic stall,
    output logic [31:0] instr_compressed
);
    c_extension_unit extension_unit (
        .clk_i(clk),
        .reset_i(reset),
        .pc_i(pc),
        .instr_i(instr),
        .br_taken_i(br_taken),
        .pc_realigned_o(pc_realigned),
        .pc_half_o(pc_half),
        .stall_o(stall),
        .instr_o(instr_compressed)
    );
endmodule
```

Please note that this is just a template, and you may need to modify it based on your specific requirements and design.

