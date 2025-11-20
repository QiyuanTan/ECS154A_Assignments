# Building a single bus 8-bit CPU
![CPU](CPU.png)
## You will be building a cpu with the following specifications:

The RAM content is provided in [RAMcontent](RAMcontent) file. It will contain both instructions and data.

We are going to show from beginning to end how to wire up a generic 8-bit machine.  This machine will use a 2-operand format, meaning that instructions are of the time `A=A+B`.  So, for example, `Add r0, r1` is `r0=r0+r1`.

The machine is byte-addressable. Offsets are **sign-extended**, and jumps are done by adding the sign-extended offset field to the PC.  Immediates are **not sign-extended**.

The tri-state device is controlled buffer in logisim.

## Hint: 
- You may want IR and MDR registers to be level triggered.
- Use this [example](genericcpu.pdf) from class as a guide.
- You will find the `|window| -> |combinational analysis|` feature very helpful.
- Appeartly you can use timing diagram in logisim-evolution 4.0 to help debug the werid timings.

## The machine has 3 different instruction formats:  A, B, and C.

A-type: 
| Opcode |  ds  |   s   | extra  |
|--------|------|-------|--------|
| 7-4    |  3   |   2   | 1-0    |

B-type: 
| Opcode |  ds  |   Immediate   |
|--------|------|---------------|
| 7-4    |  3   |     2-0       |

C-type: 
| Opcode |  Offset   |
|--------|-----------|
| 7-4    |   3-0     |

## The ALU can perform 4 functions:

| Operation       | ALU0    | ALU1    |
|-----------------|---------|---------|
| Add             | 1       | 1       |
| Sub             | 1       | 0       |
| Mult            | 0       | 1       |
| Nand            | 0       | 0       |

## Here are a few of the instructions that have been defined:
| Instruction Format | Opcode | Operation |
|--------------------|--------|-----------|
| nand               | 0000   | rds=~(rds & rs) |
| add                | 0001   | rds=rds+rs |
| addm               | 0010   | rds=rds+mem[rs] |
| addi               | 0011   | rds=rds+imm |
| sub                | 0100   | rds=rds-rs |
| jmp                | 1111   | PC=PC+offset (offset is sign extended) |

## RAM contents in assembly format
```
addi r0, 5      ; R0 = 5                                              ; 0011 0101
addi r1, 7      ; R1 = 7                                              ; 0011 1111
add r0, r1      ; R0 = R0 + R1 = 12                                   ; 0001 0100
sub r1, r0      ; R1 = R1 - R0 = -5                                   ; 0100 1000
addi r0, 4      ; R0 = R0 + 4 = 16                                    ; 0011 0100
addm r1, r0     ; R1 = R1 + mem[R0] = -5 + mem[16] = -5 + 117 = 112   ; 0010 1000
jmp 7           ; Jump to instruction at PC + 7                       ; 1111 0111
```

## Autograder/Testing your work
1. Read the **commandline verification** section in user guide
2. Include the following circuit in your solution for autograder to work:

![autograder things](autograder_things.png)

3. Name your file `CPU.circ`
4. Run the following command `java -jar logisim-evolution.jar grader/CPU/CPU.circ -tty table -load testdata/CPU/RAMcontent` 
5. Compare your output with [expected output](output)
