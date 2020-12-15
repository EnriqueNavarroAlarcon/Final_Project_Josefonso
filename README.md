# Safebox

### Team Members
José Elias Ferreira Afonso  
Enrique Navarro Alarcon  
[Project Github Repository](https://github.com/zecarx7/Final_Project_Josefonso)  

# Objectives

Description of 4x3 keyboard control. Application of the door lock system; display; door unlock relay; own library for keypad scanning; time limit for entering the correct 4-digit code; several correct codes to distinguish more people; signaling of entering the wrong code; time delay for re-locking the lock approx. 2 to 3 seconds; sending interesting information about the status of the application to the UART.



# Hardware description


#### Schematic: 
![enter image description here](https://raw.githubusercontent.com/zecarx7/Final_Project_Josefonso/main/CircuitoSafeBox.PNG?token=ARDN7TS3KG3PIXHVNKIPXRK73DK4Q)




# Code description and simulations

### Keypad.c Description

    while(1){
	for(int col=0;col<3;col++){
		PORTB |= (1<<col);
		_delay_ms(50);
		if(PINB & (1 << PINB3)){ 
			if(col==0){
			
	            while(PINB & (1 << PINB3));
					PORTB &= ~(1<<col);
				return 'N'; 
				
			}
			if(col==1){
				
	            while(PINB & (1 << PINB3));
				PORTB &= ~(1<<col);
				return '0';
			}
			if(col==2){
		
	        while(PINB & (1 << PINB3));
				PORTB &= ~(1<<col);
				return 'N';
			}
		}
		if(PINB & (1 << PINB4)){
			if(col==0){
	while(PINB & (1 << PINB4));
	PORTB &= ~(1<<col);
				return '7';
			}
			if(col==1){
						while(PINB & (1 << PINB4));
						PORTB &= ~(1<<col);
				return '8';
			}
			if(col==2){		
				while(PINB & (1 << PINB4));
				PORTB &= ~(1<<col);
				return '9';
			}
		}
		if(PINB & (1 << PINB5)){
			if(col==0){
					while(PINB & (1 << PINB5));
					PORTB &= ~(1<<col);
				return '4';
			}
			if(col==1){
						while(PINB & (1 << PINB5));
						PORTB &= ~(1<<col);
				return '5';
			}
			if(col==2){
						while(PINB & (1 << PINB5));
						PORTB &= ~(1<<col);
				return '6';
			}
		}
		if(PINB & (1 << PINB6)){
			if(col==0){
						while(PINB & (1 << PINB6));
						PORTB &= ~(1<<col);
				return '1';
			}
			if(col==1){
						while(PINB & (1 << PINB6));
						PORTB &= ~(1<<col);
				return '2';
			}
			if(col==2){
				while(PINB & (1 << PINB6));
				PORTB &= ~(1<<col);
				return '3';
			}
		}
		
		PORTB &= ~(1<<col);
	}
	_delay_ms(50);
	}

This is the script for reading a keypad input. It applies 5V to each column (OUPUT PINS) of the keypad in a for loop. Then waits for the user to release the key (while(PINB & (1 << PINB_NUMBER));) and returns the key value by checking which row as 5V which means that the current is passing by the user closing the circuit with the press of a button

### Automatic lock Description

    //Waits 3 seconds and locks again

			PORTC |= (1<<PORTC0);
			TCCR1B |= (1<<CS10) | (1<<CS12);
			OCR1A = 46875;// fui calculador para ter 3 segundos esse e codico 46875
			TCNT1 = 0;
				
			//Locker timeout
			while((TIFR1 & (1<<OCF1A)) == 0);// wait till the timer overflow flag is SET
			PORTC ^= (1<< PORTC0);
			TCNT1 = 0;
			TCCR1B = 0X00;

			TIFR1 |= (1<<OCF1A) ; //clear timer1 overflow flag
			lcd_clrscr();

To implement the automatic lock we used TIMER1 16 bit timer for ATMega328p and calculated using a online calculator [AVR Timer Calculator](https://eleccelerator.com/avr-timer-calculator/)
The compare value 46875 using a prescaler of CLK/1024 which then triggers the flag when 3 seconds have passed. The timer is initiated and stopped by setting the 	Prescaler: 	TCCR1B |= (1<<CS10) | (1<<CS12); starts the count and TCCR1B = 0X00; stops the count.


### Simulation 1 - Correct code -> Open
This figure presents the simulation of saving the code in the EEPROM and then inserting the correct code. An automatic lock is implemented after 3 seconds of inserting the correct code using TIMER1
 
![enter image description here](https://raw.githubusercontent.com/zecarx7/Final_Project_Josefonso/main/Simu1.gif)

## Simulation 2 - Wrong code
This simulation shows the user inputing a wrong code. A message is shown in the LCD and the program asks the user to input a new code.

![enter image description here](https://raw.githubusercontent.com/zecarx7/Final_Project_Josefonso/main/Simu2.gif)  


# Video/Animation
This gif presents all the features of the presented work:  
	- Saving one pin in the EEPROM  
	- Open relay only with correct code  
	- Automatic closes the lock after 3 seconds from opening  
	- Shows a message when the user inserts the wrong code  

![enter image description here](https://raw.githubusercontent.com/zecarx7/Final_Project_Josefonso/main/Wrong-Correct.gif)
# References
[LCD Library - Peter Fleury](http://www.peterfleury.epizy.com/avr-lcd44780.html)  
[Keypad 4x3 Working Principle](https://lastminuteengineers.com/arduino-keypad-tutorial/)  
[ATMega328 Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/Atmel-7810-Automotive-Microcontrollers-ATmega328P_Datasheet.pdf)  
[Digital Electronics 2 course Github](https://github.com/tomas-fryza/Digital-electronics-2)  