/*
 * Prelab 4.c
 *
 * Created: 4/04/2024 21:44:31
 * Author : esteb
 */ 
#define F_CPU 16000000

#include <avr/io.h>
#include <util/delay.h>
#include <avr/interrupt.h>

void initADC(void);		// Configuracion del ADC


uint8_t mylist[] = {0x3F, 0x06, 0x5B, 0x4F, 0x66, 0x6D, 0x7D, 0x07, 0x7F, 0x6F, 0x77, 0x7C, 0x39, 0x5E, 0x79, 0x71};
int low;
int lowL4;
int lowH4;
int conteo;

int main(void)
{
	cli();
	initADC();
	sei();

	 //Configuración de interrupciones
	 PCICR |= (1 << PCIE0);
	 PCMSK0 |= (1 << PCINT0)| (1 << PCINT1);
	  
	//Configuración de puertos 
	DDRD |= 0xFF;
	PORTD = 0;
	UCSR0B = 0;
	
	DDRB |= 0b00111100;
	
	DDRC |= 0xFF;
   
    while (1) 
	{
		//Comparación de la variable conteo para encender los leds en el puerto D	
		if(conteo > 255)
		conteo = 0;
	
		if (conteo < 0)
		conteo = 255;
	
		PORTD = conteo;
	
		//Obtencion del valor del potenciometro para analizarlo en el Puerto C7
		ADCSRA |= (1<<ADSC);
		
		_delay_ms(4);
		//Multiplexado de los displays 
		PORTB = (1<<PORTB4);
		lowL4 = low & 0x0F;
		PORTC = mylist[lowL4];
		//segun sea el numero de la variable enciende el puerto B5 para poder accionar el segmento G del display 
		switch (lowL4)
		{
			case 0:
			PORTB |= (0<<PORTB5);
			break;
			case 1:
			PORTB |= (0<<PORTB5);
			break;
			case 2:
			PORTB |= (1<<PORTB5);
			break;
			case 3:
			PORTB |= (1<<PORTB5);
			break;
			case 4:
			PORTB |= (1<<PORTB5);
			break;
			case 5:
			PORTB |= (1<<PORTB5);
			break;
			case 6:
			PORTB |= (1<<PORTB5);
			break;
			case 7:
			PORTB |= (0<<PORTB5);
			break;
			case 8:
			PORTB |= (1<<PORTB5);
			break;
			case 9:
			PORTB |= (1<<PORTB5);
			break;
			case 10:
			PORTB |= (1<<PORTB5);
			break;
			case 11:
			PORTB |= (1<<PORTB5);
			break;
			case 12:
			PORTB |= (0<<PORTB5);
			break;
			case 13:
			PORTB |= (1<<PORTB5);
			break;
			case 14:
			PORTB |= (1<<PORTB5);
			break;
			case 15:
			PORTB |= (1<<PORTB5);
			break;
		}
		
	_delay_ms(4);
		//Multiplexado de los displays 
		PORTB = (1<<PORTB3);
		lowH4 = (low>>4) & 0x0F;
		//segun sea el numero de la variable enciende el puerto B5 para poder accionar el segmento G del display

		switch (lowH4)
		{
			case 0:
			PORTB |= (0<<PORTB5);
			break;
			case 1:
			PORTB |= (0<<PORTB5);
			break;
			case 2:
			PORTB |= (1<<PORTB5);
			break;
			case 3:
			PORTB |= (1<<PORTB5);
			break;
			case 4:
			PORTB |= (1<<PORTB5);
			break;
			case 5:
			PORTB |= (1<<PORTB5);
			break;
			case 6:
			PORTB |= (1<<PORTB5);
			break;
			case 7:
			PORTB |= (0<<PORTB5);
			break;
			case 8:
			PORTB |= (1<<PORTB5);
			break;
			case 9:
			PORTB |= (1<<PORTB5);
			break;
			case 10:
			PORTB |= (1<<PORTB5);
			break;
			case 11:
			PORTB |= (1<<PORTB5);
			break;
			case 12:
			PORTB |= (0<<PORTB5);
			break;
			case 13:
			PORTB |= (1<<PORTB5);
			break;
			case 14:
			PORTB |= (1<<PORTB5);
			break;
			case 15:
			PORTB |= (1<<PORTB5);
			break;
		}
		PORTC = mylist[lowH4];
		
		//comparador del valor del potenciometro con el valor del los leds, si el pot es mayor que los leds se enciende una alarma, si no pasa se queda apagada
		if (conteo < low){
			PORTB |= (1<<PORTB2);
		}
		if (conteo > low){
			PORTB |= (0<<PORTB2);
		}
    }
		
}


void initADC(void)
{
	// Seleccion de Canal ADC0
	ADMUX = 7;
	
	// Utilizando AVCC = 5V internos
	ADMUX |= (1<<REFS0);
	ADMUX &= ~(1<<REFS1);
	
	// Justificacion a la Izquierda
	ADMUX |= (1<<ADLAR);
	
	ADCSRA = 0;
	
	ADCSRA |= (1<<ADEN);
	//Habilitamos las interrupciones
	ADCSRA |= (1<<ADIE);
	
	// Habilitamos el Prescaler de 128
	ADCSRA |= (1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0);
	
	// Habilitando el ADC
	ADCSRA |= (1<<ADEN);
}

//vector de interrupciones para el ADC
ISR(ADC_vect){
	low = ADCH;
	ADCSRA |= (1<<ADIF);

}
//Interrupcion de los pushbuttons en el puerto B
ISR(PCINT0_vect){
	
	
	if ((PINB&(1<<PINB1))==0) //Reviso el botón de incremento
	conteo++;
	

	
	if ((PINB&(1<<PINB0))==0) //Reviso el botón de decremento

	conteo--;
}
