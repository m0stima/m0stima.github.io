---
title: x64 asm ; exercise 1B ; Windows x64 calling convention and basic stack frames
---

```nasm
; calling_stack.asm - exercise 1B: Windows x64 calling convention and basic stack frames

default rel                                 ; Use RIP-relative addressing where it applies
extern ExitProcess                          ; Import ExitProcess from Kernel32

section .data                               ; Data to inspect after running
    ret_sum1    dq 0                        ; Storage for return value of sum3(10,30,30)
    ret_sum2    dq 0                        ; Storage for return value of sum3_stack(1,2,3)
    ret_sum3    dq 0                        ; Storage for return value of sum3_calls()

section .text
global main                                 ; Exported program entry point
main:                                       ; Custom entry (no CRT is used)

    sub     rsp, 28h                        ; Reserve 40 bytes: 32 for shadow space + 8 to keep 16-byte alignment

    ; Call a leaf function (no stack frame inside the calee)
    mov     ecx, 10                         ; RCX = first integer argument (a)
    mov     edx, 20                         ; RDX = second integer argument (b)
    mov     r8d, 30                         ; R8D = third integer argument (c)
    call    sum3                            ; Call sum3(a,b,c); result returned in EAX
    mov     [ret_sum1], eax                 ; Store result of sum3 into ret_sum1

    ; Call a function that uses a stack frame and one local variable
    mov     ecx, 1                          ; RCX = a
    mov     edx, 2                          ; RDX = b
    mov     r8d, 3                          ; R8D = c
    call    sum3_stack                      ; Call framed version; result returned in EAX
    mov     [ret_sum2], eax                 ; Store result into ret_sum2

    ; Call a function that itself performs a CALL (demonstrates caller setup)
    call    sum3_calls                      ; This function will call sum3(100,200,300)
    mov     [ret_sum3], eax                 ; Store result into ret_sum3

    xor     ecx, ecx                        ; RCX = process exit code (0)
    call    ExitProcess                     ; Terminate the process

; sum3: leaf function (does not build a stack frame)
; Signature: int sum3(int a, int b, int c)
; Windows x64: RCX=a, RDX=b, R8=c; return value in EAX
global sum3
sum3:
    mov     eax, ecx                        ; EAX = a
    add     eax, edx                        ; EAX = a + b
    add     eax, r8d                        ; EAX = a + b + c
    ret                                     ; Return to caller

; sum3_stack: function with a stack frame and one 32-bit local
; Uses RBP as a frame pointer; keeps stack aligned for potential calls
global sum3_stack
sum3_stack:
    push    rbp                             ; Save caller's RBP
    mov     rbp, rsp                        ; Establish RBP as frame pointer
    sub     rsp, 20h                        ; Reserve 32 bytes (locals / alingment)

    mov     dword [rbp-4], ecx              ; local tmp = a
    add     dword [rbp-4], edx              ; tmp += b
    mov     eax, dword [rbp-4]              ; EAX = tmp
    add     eax, r8d                        ; EAX = tmp + c

    leave                                   ; Restore RSP and RBP (mov rsp,rbp; pop rbp)
    ret                                     ; Return to caller

; sum3_calls: function that prepares arguments and calls another function
; Demonstrates caller responsabilities (alignment and shadow space)
global sum3_calls
sum3_calls:
    push    rbp                             ; Save caller's RBP
    mov     rbp, rsp                        ; Establish RBP as frame pointer
    sub     rsp, 20h                        ; Reserve 32 bytes (shadow space for outgoing call)

    mov     ecx, 100                        ; RCX = first argument for sum3
    mov     edx, 200                        ; RDX = second argument for sum3
    mov     r8d, 300                        ; R8D = third argument for sum3
    call    sum3                            ; Call sum3(100,200,300); EAX receives the result (600)

    leave                                   ; Restore RSP and RBP
    ret                                     ; Return to caller
```
