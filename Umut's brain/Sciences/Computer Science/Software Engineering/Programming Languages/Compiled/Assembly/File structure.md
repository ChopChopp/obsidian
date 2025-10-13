```assembly
;Directives
        PRESERVE8
        THUMB
```

**PRESERVE8**: Stack alignment directive
- 

**THUMBS**: Instruction set directive
- Tells the assembler to use Thumb instruction set (16-bit instructions)
- ARM processors can run two modes, ARM mode (32-bit) or Thumb mode (16-bit)
- Thumb instructions are more compact and efficient for memory
- Most modern Cortex-M microcontrollers use Thumb-2 (a mix of 16-bit and 32-bit instructions)
