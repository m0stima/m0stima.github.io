---
title: x64 asm ; exercise 1E ; Copy N bytes using 'rep movsb' inside a callee that preserves RSI/RDI
---

```nasm
; memcpy_rep.asm - exercise 1E: Copy N bytes using 'rep movsb' inside a callee that preserves RSI/RDI

default rel                                 
extern ExitProcess                          

section .data
    src_buf     db 10,20,30,40,50           ; Source buffer (5 bytes)
    src_len     equ $ - src_buf             ; Number of bytes to copy
    dst_buf     times src_len db 0          ; Destination buffer initialized to zero
    sum_dst     dq 0                        ; Sum of destination bytes after copy

section .text
global main
main:

    sub     rsp, 28h                        ; Shadow space + aligment

    ; Call memcpy_basic(dst, src, len)
    lea     rcx, [rel dst_buf]              ; RCX = dst
    lea     rdx, [rel src_buf]              ; RDX = src
    mov     r8d, src_len                    ; R8D = length
    call    memcpy_basic                    ; Copy bytes; RAX returns dst

    ; Compute a simple sum over dst to have a numeric result to inspect
    lea     rsi, [rel dst_buf]              ; RSI = &dst_buf
    xor     rax, rax                        ; RAX = running sum
    xor     rcx, rcx                        ; RCX = i = 0

.sum_loop:
    cmp     ecx, src_len                    ; if (i >= len) break
    jae     .sum_done
    movzx   rdx, byte [rsi + rcx]           ; RDX = dst[i]
    add     rax, rdx                        ; sum += dst[i]
    inc     rcx                             ; i++
    jmp     .sum_loop

.sum_done:
    mov     [sum_dst], rax                  ; Store sum of destination bytes

    xor     ecx, ecx                        ; Exit code = 0
    call    ExitProcess

; void* memcpy_basic(void* dst, const void* src, size_t len)
; Windows x64 args: RCX=dst, RDX=src, R8=len. Return RAX=dst
; Preserves callee-saved RSI/RDI. Uses 'rep movsb'
global memcpy_basic
memcpy_basic:
    push    rdi                             ; Save callee-saved registers to modify
    push    rsi

    mov     rdi, rcx                        ; RDI = dst (rep movsb destination)
    mov     rsi, rdx                        ; RSI = src (rep movsb source)
    mov     rcx, r8                         ; RCX = len (rep count)
    cld                                     ; Ensure DF=0 (forward direction)
    rep     movsb                           ; Copy RCX bytes from [RSI] to [RDI]
    mov     rax, rdi                        ; RAX = dst (common memcpy semantics)

    pop     rsi                             ; Restore callee-saved regs
    pop     rdi
    ret
```
