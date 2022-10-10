# FirstTouch
Знакомство
extern _printf

section .rodata
answer db "наибольшее значение аргумента: %llu",10,0

section .text
global _main:
_main:
    push rbp
    mov rbp, rsp
    sub rsp, 16
    and rsp, -15 
    mov rax, 1 
    mov rcx, 1
.loop:
    mul rcx
    jc .end ;выходим из цикла, если случилось переполнение
    inc rcx ;увечиливаем значение текущего аргумента
    jmp .loop
.end:
    mov rdi, answer ;первый аргумент - форматная строка
    mov rsi, rcx ;второй аргумент - значение для печати
    call _printf ;печатаем результат
    xor rax, rax
    leave
    ret
.ret:
    ret
