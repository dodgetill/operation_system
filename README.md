# operation_system
#Лабораторные работы по дисциплине Операционные системы
##Лабораторная работа №1
##Часть 1. Реализация функции: подсчет числа Фибоначчи
###1)Cоздадим файлы для кода и вставим его
![](https://github.com/dodgetill/operation_system/blob/main/image/1.png)
###2)После компиляции и генерации ассемблера получаем ассемблерный код:
###O0:
```
cat > factorial_commented.s << 'EOF'
        .file   "factorial.cpp"
        .text
        .local  _ZStL8__ioinit
        .comm   _ZStL8__ioinit,1,1   # Глобальная переменная для инициализации iostream

        .globl  _Z9factoriali         # Функция factorial(int) — видима снаружи
        .type   _Z9factoriali, @function
_Z9factoriali:                        # Начало функции factorial(int n)
.LFB1761:
        .cfi_startproc
        pushq   %rbp                  # Сохраняем базовый указатель стека
        movq    %rsp, %rbp            # Устанавливаем новый фрейм стека
        pushq   %rbx                  # Сохраняем регистр rbx (будет использоваться)
        subq    $24, %rsp             # Выделяем 24 байта на стеке для локальных переменных
        movl    %edi, -20(%rbp)       # Сохраняем аргумент n (передан в edi) на стек

        cmpl    $1, -20(%rbp)         # Сравниваем n с 1 (проверка базового случая)
        jg      .L2                   # Если n > 1 — переходим к рекурсии
        movl    $1, %eax              # Базовый случай: n <= 1, возвращаем 1
        jmp     .L3                   # Переходим к концу функции
.L2:                                  # Рекурсивная ветка
        movl    -20(%rbp), %eax       # Загружаем n в eax
        movslq  %eax, %rbx            # Расширяем n до 64 бит, сохраняем в rbx
        movl    -20(%rbp), %eax       # Снова загружаем n
        subl    $1, %eax              # Вычисляем n-1
        movl    %eax, %edi            # Передаём n-1 как аргумент следующему вызову
        call    _Z9factoriali         # Рекурсивный вызов factorial(n-1)
        imulq   %rbx, %rax            # Умножаем результат на n: rax = n * factorial(n-1)
.L3:                                  # Выход из функции
        movq    -8(%rbp), %rbx        # Восстанавливаем rbx
        leave                         # Восстанавливаем стек (mov rsp,rbp + pop rbp)
        ret                           # Возвращаемся из функции
        .cfi_endproc

        .section        .rodata       # Секция констант (только чтение)
.LC0:
        .string "Введите число: "     # Строка-приглашение ввода
.LC1:
        .string "Factorial "          # Строка вывода
.LC2:
        .string " = "                 # Строка вывода

        .text
        .globl  main
        .type   main, @function
main:                                 # Начало функции main
.LFB1762:
        .cfi_startproc
        pushq   %rbp                  # Сохраняем базовый указатель
        movq    %rsp, %rbp            # Новый фрейм стека
        pushq   %rbx                  # Сохраняем rbx
        subq    $24, %rsp             # Выделяем память для локальных переменных

        leaq    .LC0(%rip), %rax      # Загружаем адрес строки "Введите число: "
        movq    %rax, %rsi            # Передаём строку как аргумент
        leaq    _ZSt4cout(%rip), %rax # Загружаем cout
        movq    %rax, %rdi            # Передаём cout как первый аргумент
        call    _ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@PLT  # cout << "Введите число: "

        leaq    -20(%rbp), %rax       # Адрес переменной n на стеке
        movq    %rax, %rsi            # Передаём адрес n
        leaq    _ZSt3cin(%rip), %rax  # Загружаем cin
        movq    %rax, %rdi
        call    _ZNSirsERi@PLT        # cin >> n (читаем число с клавиатуры)

        leaq    .LC1(%rip), %rax      # Загружаем "Factorial "
        movq    %rax, %rsi
        leaq    _ZSt4cout(%rip), %rax
        movq    %rax, %rdi
        call    _ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@PLT  # cout << "Factorial "

        movq    %rax, %rdx
        movl    -20(%rbp), %eax       # Загружаем n
        movl    %eax, %esi
        movq    %rdx, %rdi
        call    _ZNSolsEi@PLT         # cout << n (выводим число)

        movq    %rax, %rdx
        leaq    .LC2(%rip), %rax      # Загружаем " = "
        movq    %rax, %rsi
        movq    %rdx, %rdi
        call    _ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@PLT  # cout << " = "

        movq    %rax, %rbx
        movl    -20(%rbp), %eax       # Загружаем n для вызова factorial
        movl    %eax, %edi
        call    _Z9factoriali         # Вызываем factorial(n)

        movq    %rax, %rsi            # Результат factorial(n) в rsi
        movq    %rbx, %rdi
        call    _ZNSolsEx@PLT         # cout << factorial(n)

        # Вывод endl (перевод строки + flush)
        movq    _ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_@GOTPCREL(%rip), %rdx
        movq    %rdx, %rsi
        movq    %rax, %rdi
        call    _ZNSolsEPFRSoS_E@PLT  # cout << endl

        movl    $0, %eax              # Возвращаем 0 (успешное завершение)
        movq    -8(%rbp), %rbx        # Восстанавливаем rbx
        leave                         # Восстанавливаем стек
        ret                           # Выход из main
        .cfi_endproc
EOF
```

```
# Исходный файл: factorial.cpp
.file   "factorial.cpp"

# Секция кода для шаблонной функции do_widen (char_traits)
.text
.section        .text._ZNKSt5ctypeIcE8do_widenEc,"axG",@progbits,_ZNKSt5ctypeIcE8do_widenEc,comdat

# Выравнивание (2 байта)
.align 2
.p2align 4

# Символ является слабым (weak) — может быть переопределён
.weak   _ZNKSt5ctypeIcE8do_widenEc
.type   _ZNKSt5ctypeIcE8do_widenEc, @function

# Начало функции: std::ctype<char>::do_widen(char)
_ZNKSt5ctypeIcE8do_widenEc:
.LFB1535:                           # Local Function Begin
.cfi_startproc                      # Начало информации для исключений (CFI)
    movl    %esi, %eax              # Аргумент (char) из ESI в EAX (возвращаемое значение)
    ret                             # Возврат из функции
.cfi_endproc                        # Конец CFI
.LFE1535:                           # Local Function End
.size   _ZNKSt5ctypeIcE8do_widenEc, .-_ZNKSt5ctypeIcE8do_widenEc

# Основная секция кода
.text
.p2align 4                          # Выравнивание на 16 байт

# Глобальный символ factorial(int)
.globl  _Z9factoriali
.type   _Z9factoriali, @function

# Реализация factorial (вычисление факториала)
_Z9factoriali:
.LFB1782:
.cfi_startproc
    movl    $1, %edx                # result = 1 (начальное значение)
    cmpl    $1, %edi                # сравнить аргумент n (EDI) с 1
    jle     .L3                     # если n <= 1, перейти к возврату (факториал 0 или 1 = 1)

    movslq  %edi, %rcx              # расширить EDI → RCX (n, 64 бита)
    leal    -2(%rdi), %edx          # edx = n - 2 (счётчик итераций?)
    leaq    -1(%rcx), %rax          # rax = n - 1
    movq    %rax, %rsi              # rsi = n - 1
    subq    %rdx, %rsi              # rsi = (n-1) - (n-2) = 1 (условие остановки)
    movl    $1, %edx                # сброс edx = 1 (результат)
    jmp     .L6                     # переход в цикл

.p2align 4,,10
.p2align 3
.L7:                                # тело цикла
    subq    $1, %rax                # rax-- (декремент счётчика)
.L6:
    imulq   %rcx, %rdx              # result *= rcx (умножение)
    movq    %rax, %rcx              # rcx = rax (новый множитель)
    cmpq    %rsi, %rax              # сравнить rax с 1
    jne     .L7                     # если не равно 1 — повторять

.L3:                                # выход из функции
    movq    %rdx, %rax              # вернуть результат (RAX)
    ret
.cfi_endproc
.LFE1782:
.size   _Z9factoriali, .-_Z9factoriali

# Секция read-only строк
.section        .rodata.str1.1,"aMS",@progbits,1
.LC0:
    .string "\320\222\320\262\320\265\320\264\320\270\321\202\320\265 \321\207\320\270\321\201\320\273\320\276: "
    # "Введите число: " в UTF-8 (кириллица)
.LC1:
    .string "Factorial "             # "Factorial "
.LC2:
    .string " = "                    # " = "

# Секция запуска (main)
.section        .text.startup,"ax",@progbits
.p2align 4
.globl  main
.type   main, @function

main:
.LFB1783:
.cfi_startproc
    pushq   %rbp                    # сохранить старый RBP
    .cfi_def_cfa_offset 16
    .cfi_offset 6, -16
    leaq    .LC0(%rip), %rsi        # аргумент: строка "Введите число: "
    pushq   %rbx                    # сохранить RBX
    .cfi_def_cfa_offset 24
    .cfi_offset 3, -24
    leaq    _ZSt4cout(%rip), %rbx  # RBX = std::cout
    movq    %rbx, %rdi              # RDI = std::cout
    subq    $24, %rsp
    .cfi_def_cfa_offset 48
    call    _ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@PLT
    # вызов operator<<(cout, "Введите число: ")

    leaq    12(%rsp), %rsi          # адрес локальной переменной (int)
    leaq    _ZSt3cin(%rip), %rdi  # std::cin
    call    _ZNSirsERi@PLT          # operator>>(cin, число)

    leaq    .LC1(%rip), %rsi        # строка "Factorial "
    movq    %rbx, %rdi              # cout
    call    _ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@PLT
    # вывод "Factorial "

    movl    12(%rsp), %esi          # загрузить введённое число
    movq    %rax, %rdi              # результат предыдущего вывода (cout)
    call    _ZNSolsEi@PLT           # operator<<(cout, число)

    leaq    .LC2(%rip), %rsi        # строка " = "
    movq    %rax, %rdi              # cout
    call    _ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@PLT
    # вывод " = "

    movq    %rax, %rdi              # cout
    movl    12(%rsp), %eax          # число n
    cmpl    $1, %eax
    jle     .L18                    # если n <= 1, факториал = 1

    # Ручное вычисление факториала в main (почти как factorial)
    movslq  %eax, %rcx              # rcx = n
    subl    $2, %eax                # eax = n-2
    movl    $1, %esi                # result = 1
    leaq    -1(%rcx), %rdx          # rdx = n-1
    movq    %rdx, %r8
    subq    %rax, %r8               # r8 = 1 (условие остановки)
    jmp     .L14

.p2align 4,,10
.p2align 3
.L19:
    subq    $1, %rdx                # декремент rdx
.L14:
    imulq   %rcx, %rsi              # result *= rcx
    movq    %rdx, %rcx              # rcx = rdx (следующее число)
    cmpq    %rdx, %r8               # сравнить с 1
    jne     .L19                    # если не 1 — повторить

.L13:
    call    _ZNSo9_M_insertIxEERSoT_@PLT
    # вывод 64-битного результата факториала

    movq    %rax, %rbx              # сохранить cout
    movq    (%rax), %rax
    movq    -24(%rax), %rax
    movq    240(%rbx,%rax), %rbp    # получить указатель на ctype<char>
    testq   %rbp, %rbp
    je      .L20                    # если нет — ошибка

    cmpb    $0, 56(%rbp)            # проверить, инициализирован ли ctype
    je      .L11                    # если нет — инициализировать

    movsbl  67(%rbp), %esi          # взять символ '\n' из ctype
.L12:
    movq    %rbx, %rdi              # cout
    call    _ZNSo3putEc@PLT         # cout.put('\n')
    movq    %rax, %rdi
    call    _ZNSo5flushEv@PLT       # cout.flush()
    addq    $24, %rsp
    .cfi_remember_state
    .cfi_def_cfa_offset 24
    xorl    %eax, %eax              # return 0
    popq    %rbx
    .cfi_def_cfa_offset 16
    popq    %rbp
    .cfi_def_cfa_offset 8
    ret

.L11:                               # инициализация ctype
    .cfi_restore_state
    movq    %rbp, %rdi
    call    _ZNKSt5ctypeIcE13_M_widen_initEv@PLT
    movq    0(%rbp), %rax
    movl    $10, %esi               # '\n'
    leaq    _ZNKSt5ctypeIcE8do_widenEc(%rip), %rdx
    movq    48(%rax), %rax
    cmpq    %rdx, %rax
    je      .L12                    # если do_widen по умолчанию

    movl    $10, %esi
    movq    %rbp, %rdi
    call    *%rax                   # вызвать do_widen
    movsbl  %al, %esi
    jmp     .L12

.L18:                               # факториал от 0 или 1 равен 1
    movl    $1, %esi
    jmp     .L13

.L20:                               # ошибка bad_cast
    call    _ZSt16__throw_bad_castv@PLT
.cfi_endproc
.LFE1783:
.size   main, .-main

# Глобальная конструкция (инициализация std::ios_base::Init)
.p2align 4
.type   _GLOBAL__sub_I__Z9factoriali, @function
_GLOBAL__sub_I__Z9factoriali:
.LFB2317:
.cfi_startproc
    pushq   %rbx
    .cfi_def_cfa_offset 16
    .cfi_offset 3, -16
    leaq    _ZStL8__ioinit(%rip), %rbx  # адрес глобального объекта ioinit
    movq    %rbx, %rdi
    call    _ZNSt8ios_base4InitC1Ev@PLT  # конструктор ios_base::Init
    movq    _ZNSt8ios_base4InitD1Ev@GOTPCREL(%rip), %rdi  # деструктор
    movq    %rbx, %rsi
    popq    %rbx
    .cfi_def_cfa_offset 8
    leaq    __dso_handle(%rip), %rdx
    jmp     __cxa_atexit@PLT   # зарегистрировать деструктор при завершении
.cfi_endproc
.LFE2317:
.size   _GLOBAL__sub_I__Z9factoriali, .-_GLOBAL__sub_I__Z9factoriali

# Секция инициализации: вызывается до main
.section        .init_array,"aw"
.align 8
.quad   _GLOBAL__sub_I__Z9factoriali

# Локальный статический объект для инициализации потоков ввода-вывода
.local  _ZStL8__ioinit
.comm   _ZStL8__ioinit,1,1

# Скрытый символ для корректной работы atexit
.hidden __dso_handle

# Идентификатор компилятора
.ident  "GCC: (Debian 12.2.0-14+deb12u1) 12.2.0"

# Секция, указывающая, что стек не требует исполнения
.section        .note.GNU-stack,"",@progbits
```
3)Теперь создадим Makefile и запустим его:
![](https://github.com/dodgetill/operation_system/blob/main/image/2.png)
все прекрасно создалось и работает
###Теперь осталось сделать параллельный процесс. Это добавить многопоточность к программе.
![](https://github.com/dodgetill/operation_system/blob/main/image/3.png)
###Обновляем Makefile, чтобы всё собиралось одной командой make:
![](https://github.com/dodgetill/operation_system/blob/main/image/4.png)
###Проверяем, что все собирается:
![](https://github.com/dodgetill/operation_system/blob/main/image/5.png)
###Итог:
![](https://github.com/dodgetill/operation_system/blob/main/image/6.png)
##Лабораторная работа №3a.
###Вариант 4: В текстовых файлах (. t x t ) найти заданную в параметре сценария строку, из найденных файлов составить список, сохранить его в файл.
####Создаем файл скрипта:
![](https://github.com/dodgetill/operation_system/blob/main/image/9.png)
####Вставляем код:
![](https://github.com/dodgetill/operation_system/blob/main/image/10.png)
####Делаем скрипт исполняемым и создаем тестовые файлы:
![](https://github.com/dodgetill/operation_system/blob/main/image/11.png)
![](https://github.com/dodgetill/operation_system/blob/main/image/12.png)
