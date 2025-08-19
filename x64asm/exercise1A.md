---
title: x64 asm ; Exercise 1A ; MOV/LEA and addressing over a byte array
---

```nasm
; addressing_sum.asm - Exercise 1A: MOV/LEA and addressing over a byte array

default rel                                 ; In x64 use RIP-relative addressing by default (safer/portable)
extern ExitProcess                          ; Declare ExitProcess to exit the process cleanly

section .data                               ; Initialized data section
    arr         db 1,2,3,4,5                ; Small array of 5 bytes with values 1..5
    arr_len     equ $-arr                   ; Compile-time length of arr (current location minus start of arr)
    res_sum     dq 0                        ; 8-byte variable to store the final sum
    addr_arr    dq 0                        ; Will hold the base address of arr
    addr_arr3   dq 0                        ; Will hold the address of arr+3 (4th element)

section .text                               ; Code section
global main                                 ; Export 'main' for the linker
main:                                       ; Entry point

    sub     rsp, 28h                        ; Reserve 0x28 bytes on stack:
                                            ;   - 0x20 (32) bytes shadow space required by Win64 ABI
                                            ;   - 0x8 (8) bytes to keep RSP 16-byte aligned before calls

    lea     rax, [rel arr]                  ; RAX = address of arr (Load Effective Address)
    mov     [rel addr_arr], rax             ; Store base address of arr into addr_arr

    lea     rbx, [rel arr + 3]              ; RBX = address of 4th element (offset + 3 bytes)
    mov     [rel addr_arr3], rbx            ; Store arr+3 address into addr_arr3

    mov     rsi, rax                        ; RSI = base pointer to arr
    xor     rax, rax                        ; RAX = 0 (accumulator for the sum)
    xor     rcx, rcx                        ; RCX = 0 (loop index i = 0)

.loop:                                      ; Loop label
    cmp     ecx, arr_len                    ; Compare i (ECX) with arr length
    jae     .done                           ; If i > arr_len (unsigned), exit loop

    movzx   rdx, byte [rsi + rcx]           ; RDX = zero-extended byte arr[i] (base in RSI + index RCX)
                                            ; (use movsx instead to sign-extend if needed)
    add     rax, rdx                        ; sum += arr[i]
    inc     rcx                             ; i++
    jmp     .loop                           ; Repeat loop

.done:                                      ; Loop exit
    mov     [rel res_sum], rax              ; Store finel sum into res_sum

    xor     ecx, ecx                        ; RCX = 0 (ExitProcess exit code)
    call    ExitProcess                     ; ExitProcess(0)
```
