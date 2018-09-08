;Este es un comentario, esta es una plantilla
    list p=18f4550			;Modelo del microcontrolador
    #include<p18f4550.inc>		;Llamo a la librería de nombre de los regs
    
    ;Zona de los bits de configuración (falta)
    CONFIG  FOSC = INTOSCIO_EC	  ; Oscillator Selection bits (XT oscillator (XT))
    CONFIG  PWRT = ON             ; Power-up Timer Enable bit (PWRT enabled)
    CONFIG  BOR = OFF             ; Brown-out Reset Enable bits (Brown-out Reset disabled in hardware and software)
    CONFIG  WDT = OFF             ; Watchdog Timer Enable bit (WDT disabled (control is placed on the SWDTEN bit))
    CONFIG  PBADEN = OFF          ; PORTB A/D Enable bit (PORTB<4:0> pins are configured as digital I/O on Reset)
    CONFIG  LVP = OFF             ; Single-Supply ICSP Enable bit (Single-Supply ICSP disabled)

    CONFIG  CCP2MX = ON           ; CCP2 MUX bit (CCP2 input/output is multiplexed with RC1)
    CONFIG  MCLRE = ON            ; MCLR Pin Enable bit (MCLR pin enabled; RE3 input pin disabled)
    
	org 0x0000
	goto configuro

	
	org 0x0020
configuro:
	    movlw 0x03
	    movwf TRISD          ;Puerto D0 y D1 como entradas
	    movlw 0x62
	    movwf OSCCON	;Osciloscopio configuracion de 4MH
	    bcf TRISD, 2	;Puerto D2 como salida, por donde saldrá la onda cuadrada
	    clrf TRISB          ;Todos los puertos b como salida
	    movlw 0xC8
	    movwf T0CON	

inicio:	    btfss PORTD, 0
	    goto subcondicional
	    btfss PORTD, 1
	    goto condicional3
	    goto condicional4

subcondicional:
	    btfss PORTD, 1
	    goto condicional1
	    goto condicional2

condicional1: ;00
	    bsf LATD, 2		    ;valor 1
	    movlw 0x3F		    ;diplay 0
	    movwf LATB
	    bsf T0CON, PSA	    ;encender el multiplicador x2
	    movlw .6		    ;asignar un valor inicial de 6
	    movwf TMR0L
	    bcf INTCON, TMR0IF	    ;bajar la bandera
	    call papa		    ;llamar al bucle de cuenta
	    bcf LATD, 2		    ;valor 1
	    movlw 0xC1
	    movwf T0CON		    ;asignar al timer0 una frecuancia de 1:4
	    movlw .69		    ;asinar un valor inicial de 69
	    movwf TMR0L		    
	    bcf INTCON, TMR0IF	    ;bajar la bandera
	    call papa		    ;llamar al bucle de cuenta
	    goto inicio		    ;reiniciar
	    
condicional2: ;01
	    bsf LATD, 2
	    movlw 0x06
	    movwf LATB
	    bsf T0CON, PSA
	    movlw .106
	    movwf TMR0L
	    bcf INTCON, TMR0IF
	    call papa
	    bcf LATD, 2
	    movlw 0xC0
	    movwf T0CON
	    movlw .69
	    movwf TMR0L
	    bcf INTCON, TMR0IF
	    call papa
	    goto inicio
	    
condicional3: ;10
	    bsf LATD, 2
	    movlw 0x5B
	    movwf LATB
	    movlw 0xC1
	    movwf T0CON		    
	    movlw .69	
	    movwf TMR0L
	    bcf INTCON, TMR0IF
	    call papa
	    bcf LATD, 2
	    bsf T0CON, PSA	    
	    movlw .6
	    movwf TMR0L
	    bcf INTCON, TMR0IF
	    call papa
	    goto inicio
	    
condicional4: ;11
	    bsf LATD, 2
	    movlw 0x4F
	    movwf LATB
	    goto inicio

papa:
	    btfss INTCON, TMR0IF
	    goto papa
	    return
	   
	    

	    
	    
	    end