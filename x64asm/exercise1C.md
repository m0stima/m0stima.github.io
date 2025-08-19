---
title: x64 asm ; exercise 1C ; Flags, conditional branches vs. CMOV-based selection (signed comparisons)
---

```nasm
; flags_branch.asm - exercise 1C: Flags, conditional branches vs. CMOV-based selection (signed comparisons)

default rel                                 ; Use RIP-relative addressing where it applies
extern ExitProcess                          ; Import ExitProcess from Kernel32

section .data                               ; Data to inspect after running
    a           dd 25                       ; 32-bit integer a (signed)
    b           dd 40                       ; 32-bit integer b (signed)
    res_jmp     dd 0                        ; Storage for result using branches
    res_cmov    dd 0                        ; Storage for result using CMOV

section .text
global main                                 ; Exported program entry point
main:                                       ; Custom entry (no CRT is used)

    sub     rsp, 28h                        ; Reserve 40 bytes: 32 for shadow space + 8 to keep 16-byte alignment

    ; max(a,b) using a conditional branch (signed)
    mov     eax, [a]                        ; EAX = a
    mov     edx, [b]                        ; EDX = b
    mov     ecx, eax                        ; ECX = a (kept for comparison reference if needed)
    cmp     eax, edx                        ; Set flags based on (a - b)
    jge     .keep_a                         ; If a >= b (signed), keep EAX as a
    mov     eax, edx                        ; Otherwise, EAX = b
.keep_a:
    mov     [res_jmp], eax                  ; Store selected value in res_jmp

    ; max (a,b) using CMOV (signed)
    mov     ecx, [a]                        ; ECX = a
    mov     edx, [b]                        ; EDX = b
    cmp     ecx, edx                        ; Set flags based on (a - b)
    cmovl   ecx, edx                        ; If a < b (signed), ECX = b (CMOVL = move if less, signed)
    mov     [res_cmov], ecx                 ; Store selected value in res_cmov

    xor     ecx, ecx                        ; RCX = process exit code (0)
    call    ExitProcess                     ; Terminate the process
```
