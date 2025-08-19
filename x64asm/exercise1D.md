---
title: x64 asm ; exercise 1D ; Scan a byte string until NUL (0x00), store the length
---

```nasm
; strlen.asm - exercise 1D: Scan a byte string until NUL (0x00), store the length

default rel                                 ; Use RIP-relative addressing where it applies
extern ExitProcess                          ; Import ExitProcess from Kernel32

section .data                               ; Data to inspect after running
    str_text    db "Hack the world", 0      ; Zero-terminated string
    res_len     dq 0                        ; Storage for computed length (64-bit)

section .text
global main                                 ; Exported program entry point
main:                                       ; Custom entry (no CRT is used)

    sub     rsp, 28h                        ; 32 bytes shadow space + 8 for 16-byte alignment

    lea     rsi, [rel str_text]             ; RSI = &str_len (source pointer)
    xor     rcx, rcx                        ; RCX = index i = 0

.loop:                                      ; Loop start
    movzx   rax, byte [rsi + rcx]           ; RAX = zero-extended str[i]
    test    al, al                          ; Set flags based on AL 
    jz      .done                           ; If AL == 0, end of string reached
    inc     rcx                             ; i++
    jmp     .loop                           ; Continue scanning

.done:                                      ; Loop end
    mov     [res_len], rcx                  ; Store i as length (NUL not counted)

    xor     ecx, ecx                        ; RCX = process exit code (0)
    call    ExitProcess                     ; Terminate process
```
