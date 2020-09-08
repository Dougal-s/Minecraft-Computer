# Minecraft Computer
This is a fully functional general purpose programmable computer created in minecraft purely using redstone.

## Specification

### Registers
The computer contains a 4bit program counter and 3 separate 8bit general purpose registers.

#### General Purpose Registers
| Register | size | usage |
| - | - | - |
| r0 | 8bits | Always set to 0. Writing to this register does nothing |
| r1 | 8bits | An general purpose register |
| r2 | 8bits | An general purpose register |
| r3 | 8bits | An general purpose register |

#### Specialized Registers
| Register | size | usage |
| - | - | - |
| pc | 4bits | The program counter. It can be updated via the JMP instruction. |

### ALU
The ALU is extremely basic and can only perform addition and subtraction

### I/O
The computer contains a single 8bit output port that can be written to using the DSP instruction

## Instruction Set
The instruction set is extremely simple and consists of four main instructions:

Instruction | opcode | Parameters | Usage
--- | --- | --- | ---
DSP | 00 | OPCODE: 2bits; RESERVED: 2bits; SRC: 2bits; RESERVED: 4bits | displays the contents of the register specified by `SRC`
JMP | 01 | OPCODE: 2bits; CONSTANT: 8bits | sets the program counter to `CONSTANT` (**Note**: the program counter is only 4 bits wide and so the first 4bits of `CONSTANT` are ignored)
SET | 10 | OPCODE: 2bits; DST: 2bits; CONSTANT: 6bits | sets the first 6 bits of the register specified by `DST` to `CONSTANT`
ALU | 11 | OPCODE: 2bits; DST: 2bits; SRC1: 2bits; SRC2: 2bits; FUNC: 2bits | Performs the arithmetic operation specified by `FUNC` on `SRC1` and `SRC2`, storing the output in `DST`

### ALU INSTRUCTION

The ALU instruction has the following format:

| | | | | | |
|-|-|-|-|-|-|
| 2 bits | 2 bits | 2 bits | 2 bits | 2 bits |
| OPCODE | DST    | SRC1   | SRC2   | FUNC   |

The alu instruction format performs `DST = SRC1 FUNC SRC2` where FUNC is some arithmetic instruction specified in the below table.

| FUNC | OPERATION |
| - | - |
| 00 | ADD |
| 01 | SUB |
| 10 | UNDEFINED |
| 11 | UNDEFINED |

## Example Program
The following program will output the numbers in the Fibonacci sequence until an integer overflow occurs at which point it will display the last 8bits of the rest of the Fibonacci numbers.
```
r1 = 0
r2 = 0
while (true)
	r3 = r1
	r1 = r2 + r3
	r2 = r3
	print(r1)
```

| Binary | Assembly |
| - | - |
| 10 01 000000   | SET r1 0
| 10 10 000000   | SET r2 0
|                | loop_start:
| 11 11 01 00 00 | &nbsp;&nbsp;&nbsp;&nbsp;ADD r3 r1 r0
| 11 01 10 11 00 | &nbsp;&nbsp;&nbsp;&nbsp;ADD r1 r2 r3
| 11 10 11 00 00 | &nbsp;&nbsp;&nbsp;&nbsp;ADD r2 r3 r0
| 00 00 01 0000  | &nbsp;&nbsp;&nbsp;&nbsp;DSP r1
| 01 00000011    | &nbsp;&nbsp;&nbsp;&nbsp;JMP loop_start

## TODO:
* Implement conditional jumps `jl` and `jle`. This will require increasing the opcode width and instruction width by 1
* add multiple outputs. Use the first two RESERVED bits in the display instruction to specify which port to write to.
