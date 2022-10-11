# Домашнее задание № 4
### 1) Разработать программу на языке ассемблера, определяющую максимальное значение аргумента, при котором результат вычисления факториала размещается в 64-х разрядном ### машинном слове.
#### Первый вариант на NASM (не факт, что всё правильно, но я пыталась:) )
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
#### Второй вариант (используя дизассемблирование с СИ)
    .LC0:
            .string "%d"
    main:
            push    rbp
            mov     rbp, rsp
            sub     rsp, 16
            mov     QWORD PTR [rbp-8], 1
            mov     DWORD PTR [rbp-12], 2
            jmp     .L2
    .L3:
            mov     eax, DWORD PTR [rbp-12]
            cdqe
            mov     rdx, QWORD PTR [rbp-8]
            imul    rax, rdx
            mov     QWORD PTR [rbp-8], rax
            add     DWORD PTR [rbp-12], 1
    .L2:
            mov     eax, DWORD PTR [rbp-12]
            cdqe
            imul    rax, QWORD PTR [rbp-8]
            cmp     QWORD PTR [rbp-8], rax
            jb      .L3
            sub     DWORD PTR [rbp-12], 1
            mov     eax, DWORD PTR [rbp-12]
            mov     esi, eax
            mov     edi, OFFSET FLAT:.LC0
            mov     eax, 0
            call    printf
            mov     eax, 0
            leave
            ret
### 2) Опционально. Реализовать вычисление факториала на языке ассемблера в виде подпрограммы, получающей на вход значение аргумента. Вывод результатов должна осуществлять главная функция.
#### Код на СИ
    #include <stdio.h>
    int main()
    {
        int x;
        scanf("%d", &x);
        long unsigned int answer = 1;
        while(x != 0){
            answer = answer * x;
            x = x - 1;
        }
        printf("%lu", answer);
    }
#### Код на ассемблере
    .LC0:
            .string "%d"
    .LC1:
            .string "%lu"
    main:
            push    rbp
            mov     rbp, rsp
            sub     rsp, 16
            lea     rax, [rbp-12]
            mov     rsi, rax
            mov     edi, OFFSET FLAT:.LC0
            mov     eax, 0
            call    __isoc99_scanf
            mov     QWORD PTR [rbp-8], 1
            jmp     .L2
    .L3:
            mov     eax, DWORD PTR [rbp-12]
            cdqe
            mov     rdx, QWORD PTR [rbp-8]
            imul    rax, rdx
            mov     QWORD PTR [rbp-8], rax
            mov     eax, DWORD PTR [rbp-12]
            sub     eax, 1
            mov     DWORD PTR [rbp-12], eax
    .L2:
            mov     eax, DWORD PTR [rbp-12]
            test    eax, eax
            jne     .L3
            mov     rax, QWORD PTR [rbp-8]
            mov     rsi, rax
            mov     edi, OFFSET FLAT:.LC1
            mov     eax, 0
            call    printf
            mov     eax, 0
            leave
            ret
