---
title: x64 asm ; exercise 1A ; MOV/LEA and addressing over a byte array
---

```nasm
; addressing_sum.asm - exercise 1A: MOV/LEA and addressing over a byte array

default rel                             ; Use RIP-relative addressing where it applies
extern ExitProcess                      ; Import ExitProcess from Kernel32

section .data                           ; Data to inspect after running
    arr         db 1,2,3,4,5            ; Byte array used as input
    arr_len     equ $ - arr             ; Compile-time length of the array
    res_sum     dq 0                    ; Storage for the final 64-bit sum
    addr_arr    dq 0                    ; Storage for the base address of arr
    addr_arr3   dq 0                    ; Storage for the address of arr+3

section .text
global main                             ; Exported program entry point
main:                                   ; Custom entry (no CRT is used)

    sub     rsp, 28h                    ; Reserve 40 bytes: 32 for shadow space + 8 to keep 16-byte alignment

    lea     rax, [rel arr]              ; RAX = address of arr
    mov     [addr_arr], rax             ; Save base address of arr into addr_arr

    lea     rbx, [rax + 3]              ; RBX = address of arr+3 using RAX as base
    mov     [addr_arr3], rbx            ; Save address of arr+3 into addr_arr3

    mov     rsi, rax                    ; RSI = base pointer to arr for indexed loads
    xor     rax, rax                    ; RAX = 0 (sum accumulator)
    xor     rcx, rcx                    ; RCX = 0 (loop index i)

.loop:                                  ; Start of the loop
    cmp     ecx, arr_len                ; Compare i with the array length (unsigned)
    jae     .done                       ; If i >= arr_len, exit the loop

    movzx   rdx, byte [rsi + rcx]       ; RDX = zero-extended arr[i] (load one byte)
    add     rax, rdx                    ; sum += arr[i]
    inc     rcx                         ; i = i + 1
    jmp     .loop                       ; Repeat the loop

.done:                                  ; End of the loop
    mov     [res_sum], rax              ; Store the final sum in res_sum

    xor     ecx, ecx                    ; RCX = process exit code (0)
    call    ExitProcess                 ; Terminate the process
```
