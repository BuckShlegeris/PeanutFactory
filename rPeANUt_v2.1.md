# Understanding rPeANUt v2.1
In which I document what I have learned about the rPeANUt computer and assembly language, as I learn it, in an easily-referenceable way.

Based heavily on [_Specification of the rPeANUt Computer and Assembler (v2.1)_][rPeANUt_spec].

- - -

## Registers
There are 8 writable registers, and 15 registers in total. Each register is represented by one half-byte, except the instruction register<sup>1</sup>.

| Register | Half-byte | Description          | Size   |
|----------|-----------|----------------------|--------|
| R0       | 0x0       | Writable register    | 32 bit |
| R1       | 0x1       | Writable register    | 32 bit |
| R2       | 0x2       | Writable register    | 32 bit |
| R3       | 0x3       | Writable register    | 32 bit |
| R4       | 0x4       | Writable register    | 32 bit |
| R5       | 0x5       | Writable register    | 32 bit |
| R6       | 0x6       | Writable register    | 32 bit |
| R7       | 0x7       | Writable register    | 32 bit |
| SP       | 0x8       | Stack pointer        | 16 bit |
| SR       | 0x9       | Status register      | 32 bit |
| PC       | 0xA       | Program counter      | 16 bit |
| ONE      | 0xB       | Constant: 1          |  1 bit |
| ZERO     | 0xC       | Constant: 0          |  1 bit |
| MONE     | 0xD       | Constant: -1         |  1 bit |
| IR       | N/A       | Instruction register | 32 bit |

## Instructions
Instructions and their arguments are exactly **4 bytes** in total - that is, the minimum value is `0x00000000` and the maximum value is `0xFFFFFFFF`.

## Programming on rPeANUt

Here are some examples of programs written in rPeANUt assembly, in a more easily referenced form than Eric's slides.

### Recursive function

```
;  Main program
0x0100:
	load #4 R0 ; we want to calculate factorial of 4
	push R2; push a space for the return value
	push R0; push our argument
	call fact
	pop R0; pop our argument out
	pop R2; pop the result out
	halt


; stack frame
; return address #0
; x #-1
; result #-2
fact:
	load SP #-1 R0 ; place x into R0
	jumpz R0 factif ; if x != 0 {
	
	sub R0 ONE R2
	push R7
	push R2
	
	call fact
	pop R2
	pop R7

	load SP #-1 R0
	mult R0 R7 R6
	store R6 #-2 SP
	return
factif :
	store ONE #-2 SP
	return
```

## Footnotes

1. I'm _reasonably_ sure that the instruction register can't be referenced in an instruction. The specification doesn't give them a half-byte reference, so I'm assuming that there's no way to read to or write from this register.

<!-- ----------------------------------- -->

[rPeANUt_spec]: http://cs.anu.edu.au/courses/COMP2300/rpeanut/rPeANUtv2.1spec.pdf
