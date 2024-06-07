# Hello-World-and-Blinking-LED-Board-2.2-

/* 
 * main.c 
 * 
 * Created: 5/21/2024 10:01:12 AM 
 *  Author: agarciasampedroperez 
 */  
//using usart and baud for transmissions of data 
//blinking LEDs 
//FOR BOARD V2.2 

#define F_CPU 16000000UL //this is our system clock which 16MHZ set to the variable named F_CPU 
#define USART_0_BAUD 9600//our baud rate 

#include <util/delay.h> //used to define time, something like that 
#include <avr/io.h> 
#include <string.h> 

void USART0_init (void); //configures USARTO 
void USART0_sendChar (char c); // declaration for sending character to USARTO 
void USART0_sendString (const char* str); 
void LED_init(void); 
void LED_OFF(void); 
void LED_ON(void); 

char usart_str[] = "Hello World!\r\n"; //reason why we should mostly used char is because it's a byte (8bits), and our USART is 8 bits, with 1 start and stop bit 

int main(void) 
{ 
  USART0_init();  
  LED_init(); 
  
  while(1) 
    { 
        USART0_sendString(usart_str); 
        LED_ON(); 
        _delay_ms(3000); 
        LED_OFF(); 
        _delay_ms(3000); 
    } 
} 

void LED_init(void) 
{ 
  //Port H 5th bit (PH5) GREEN LED 
  DDRH |= (1 << PH5); //Port H Data Direction Register 
  //Port H 6th bit (PH6) RED LED 
  DDRH |= (1 << PH6); //Port H Data Direction Register 
} 

 

void LED_OFF(void) 

{ 

PORTH |= (1 << PH5); //turns off GREEN LED 

PORTH |= (1 << PH6); // turns off RED LED 

} 

 

void LED_ON (void) 

{ 

PORTH &= ~(1 << PH5); //Port H Data Register GREEN LED turns on 

PORTH &= ~(1 << PH6); //Port H Data Register RED LED turns on 

} 

 

void USART0_init (void)  

{ 

UBRR0 = ((float)(F_CPU / (16 * (float)USART_0_BAUD)) - 1); //equation is found in pg 203 Baud rate 

//USART1.CTRLB |= USART_TXEN_bm; 

DDRE |= (1 <<DDE1);// Set data direction to output on PE1 

UCSR0B |= (1 << TXEN0); //Enable TX for USARTO 

 

DDRA |= (1 << DDA2); //Set data direction to output on PA2 for RS232 USART 

PORTA |= (1 << PA2); //Set PA2 to one (high) for RS323 USARTO 

} 

 

void USART0_sendChar(char c) 

{ 

//wait for empty transmit buffer... 

while (!(UCSR0A & (1<< UDRE0))) 

{ 

; 

} 

//Puts data into the buffer, sends the data through TX 

UDR0 = c; 

} 

 

void USART0_sendString(const char* str) //pointer sends address to ...  

{ 

for (size_t i = 0; i < strlen(str); i++)  

{ 

USART0_sendChar(str[i]); 

} 

} 
