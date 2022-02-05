# Labo2
si
; Archivo: main.s
; Dispositivo: PIC16F887
; Autor: Saby Andrade
; Compilador: pic-as (v2.30), MPLAB v5.40
;
; Programa: Contador de 4 bits 
; Hardware: LEDS en el puerto A;B y D pushbottons en el puerto C
;
; Creado: 31 de enero, 2022
; Última modificación: 4 de febrero, 2022
   
PROCESSOR 16F887
#include <xc.inc>

;configuration word 1
 CONFIG FOSC=INTRC_NOCLKOUT// Oscilador interno sin salidas
 CONFIG WDTE=OFF	      // WDT disabled (reinicio repetitivo del pic)
 CONFIG PWRTE=ON           // PWRT enabled (espera de 72ms al iniciar)
 CONFIG MCLRE=OFF	      // El pin de MCLR se utiliza como I/O
 CONFIG CP=OFF             // Sin protección de código
 CONFIG CPD=OFF            // Sin protección de datos
    
 CONFIG BOREN=OFF          // Sin reinicio cuando el voltaje de alimentación baja de 4v
 CONFIG IESO=OFF	      // Reinicio sin cambio de reloj interno a externo
 CONFIG FCMEN=OFF          // Cambio de reloj externo a interno en caso de fallo 
 CONFIG LVP=ON	      // Programación en bajo voltaje permitida

;configuration word 2
 CONFIG WRT=OFF            //Protección de autoescritura por el programa desactivadas
 CONFIG BOR4V=BOR40V       // Reinicio abajo de 4v, (BOR21v=2.1v)
    
 PSECT udata_bank0 ;common memory 
    cont_small: DS 1 ;1 byte
    cont_big:   DS 1 
 PSECT resVect, class=CODE, abs, delta=2
 
 ;-----------------Configuración----------------------;

main:
    CALL WATCH
    BSF STATUS, 5
    BSF STATUS, 6
    CLRF ANSEL
    CLRF ANSELH
    
    BCF STATUS, 6
    MOVLW 1Fh
    MOVWF TRISA
    
    
    BCF TRISD, 0
    BCF TRISD, 1
    BCF TRISD, 2
    BCF TRISD, 3
    BCF TRISD, 0
    BCF TRISA, 1
    BCF TRISA, 2
    BCF TRISA, 3
    BCF TRISA, 4
    BCF TRISB, 0
    BCF TRISB, 1
    BCF TRISB, 2
    BCF TRISB, 3
    
    BCF STATUS, 5
    CLRF PORTA
    CLRF PORTB
    CLRF PORTC
    CLRF PORTD
 
    
    
BOTONES:
    BTFSC PORTC, 0
    CALL SUM_1    
    BTFSC PORTC, 1
    
    CALL QUITAR_1    
    BTFSC PORTC, 2
    CALL SUM_2    
    BTFSC PORTC, 3
    CALL QUITAR_2  
    BTFSC PORTC, 4
    CALL CONTADOR
    GOTO BOTONES   
    
WATCH: 
    BSF STATUS, 5
    BSF OSCCON, 0
    BSF OSCCON, 6
    BCF OSCCON, 5
    BCF OSCCON, 4
    RETURN
    
    
    
SUM_1:
    BTFSC PORTC, 0
    goto $-1
    INCF PORTD, F
    return
    
QUITAR_1:
    BTFSS PORTC, 1
    goto $-1
    DECF PORTD, F
    return
    
SUM_2:
    BTFSS PORTC, 2
    GOTO $-1
    INCF PORTB, F
    return  
    
QUITAR_2:
    BTFSS PORTC, 3
    goto $-1
    DECF PORTB, F
    return    

CONTADOR: 
    BTFSC PORTC, 4
    goto $-1
    
    MOVF PORTD, W
    ADDWF PORTB, W
    MOVWF PORTA
    RETURN
  END
