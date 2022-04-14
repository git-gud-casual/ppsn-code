FASM BOOTLOADER

```
use16
org 0x7c00

init:
        cli
                xor ax, ax
                mov ss, ax
                mov es, ax
                mov ds, ax
                mov sp, 0x7c00
        sti
        call setTextMode

jmp start


setTextMode:
        mov ax, 0003h
        int 10h
        ret


printFromStack:
        mov ah, 02h
        int 10h
        add dl, 01h
        cmp dl, 4fh
        jbe .posSet
            mov dl, 00h
            add dh, 01h
        .posSet:
        pop ax
        cmp ax, 3fffh
        je start.exitPrintFromStack
        mov al, ah
        mov ah, 0ah
        add al, 30h
        mov cx, 0001h
        int 10h
        jmp printFromStack

toDecimal:
        push cx
        mov cx, 3fffh
        push cx
        .loop:
                div bl
                push ax
                xor ah, ah
                cmp al, 00h
                jne .loop
        jmp start.exitToDecimal

msg db "DEAD INSIDE 1000-7=ZXC",0

start:
        mov dx, 0000h
        mov bx, 000ah
        mov cx, 03e8h
        mov ax, cx
        .loop:
                jmp toDecimal
                .exitToDecimal:
                jmp printFromStack
                .exitPrintFromStack:

                mov ah, 0ah
                mov al, 2dh
                mov cx, 0001h
                int 10h

                mov ah, 02h
                int 10h
                add dl, 01h

                cmp dl, 4fh
                jbe .posSet1
                  mov dl, 00h
                  add dh, 01h
                .posSet1:

                mov ah, 0ah
                mov al, 37h
                mov cx, 0001h
                int 10h

                mov ah, 02h
                int 10h
                add dl, 01h
 
                cmp dl, 4fh
                jbe .posSet2
                  mov dl, 00h
                  add dh, 01h
               .posSet2:

                mov ah, 0ah
                mov al, 3fh
                mov cx, 0001h
                int 10h

                mov ah, 02h
                int 10h
                add dl, 02h

                cmp dl, 4fh
                jbe .posSet3
                  mov dl, 00h
                  add dh, 01h
               .posSet3:

                pop cx
                sub cx, 0007h
                mov ax, cx

                cmp cx, 03e8h
                jae .loopexit
                jmp .loop
        .loopexit:
        mov bp, msg
        mov cx, 17h
        add dh, 01h
        mov dl, 00h
        mov ax, 1301h
        int 10h

        jmp $


times 510-($-$$) db 0
dw 0xAA55
