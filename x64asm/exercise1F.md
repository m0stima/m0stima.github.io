---
title: x64 asm ; exercise 1F ; Fill N bytes using 'rep stosb' inside a callee that preserves RDI
---

```nasm
; memset_rep.asm - exercise 1F: Fill N bytes using 'rep stosb' inside a callee that preserves RDI

default rel                                 
extern ExitProcess                          

section .data
    buf         times 16 db 0               ; Buffer to fill (16 bytes)
    fill_val    db 0x41                     ; Value 'A' (65)
    fill_len    dd 8                        ; Number of bytes to fill
    sum_buf     dq 0                        ; Sum of the first 'fill_len' bytes after fill

section .text
global main
main:

    sub     rsp, 28h                        ; Shadow space + alignment

    ; Call memset_basic(dst, value, len)
    lea     rcx, [rel buf]                  ; RCX = dst
    movzx   edx, byte [rel fill_val]        ; RDX = value (use DL/AL later)
    mov     r8d, [rel fill_len]             ; R8D = len
    call    memset_basic                    ; Fill buffer

    ; Compute sum over the filled region to have a numeric result to inspect
    lea     rsi, [rel buf]                  ; RSI = &buf
    mov     ecx, [rel fill_len]             ; ECX = count
    xor     rax, rax                        ; RAX = running sum
    xor     rdx, rdx                        ; RDX = index i = 0

.sum_loop:
    cmp     edx, ecx                        ; if (i >= len) break
    jae     .sum_done
    movzx   r9, byte [rsi + rdx]            ; R9 = buf[i]
    add     rax, r9                         ; sum += buf[i]
    inc     rdx                             ; i++
    jmp     .sum_loop

.sum_done:
    mov     [sum_buf], rax                  ; Store sum (for 'A' x 8 -> 65*8 = 520)

    xor     ecx, ecx                        ; Exit code = 0
    call    ExitProcess

; void* memset_basic(void* dst, int value, size_t len)
; Windows x64 args: RCX=dst, RDX=value, R8=len. Return RAX=dst
; Preserves callee-saved RDI. Uses 'rep stobs'
global memset_basic
memset_basic:
    push    rdi                             ; Preserve callee-saved RDI

    mov     rdi, rcx                        ; RDI = dst (stosb destination)
    mov     rcx, r8                         ; RCX = len (rep count)
    mov     al, dl                          ; AL = low 8 bits of value
    cld                                     ; Ensure DF=0 (forward)
    rep     stosb                           ; Store AL into [rdi] RCX times
    mov     rax, rdi                        ; RAX = dst

    pop     rdi                             ; Restore RDI
    ret
```
