# Ciclo-de-Llenado-y-Consumo-de-Agua
Este código no es de mi autoría completa. Con permiso de los coautores, subo este código a la plataforma.

```python
import RPi.GPIO as GPIO 
import time

# Declaracion de las entradas y salidas
pm = 27
pp = 22
sa = 5
sb = 6
km1 = 21

# Variables de habilitacion
en_marcha = False
continuar = False

# Declaracion de variables
GPIO.setmode(GPIO.BCM)

GPIO.setup(pm, GPIO.IN, pull_up_down = GPIO.PUD_UP)
GPIO.setup(pp, GPIO.IN, pull_up_down = GPIO.PUD_UP)
GPIO.setup(sa, GPIO.IN)
GPIO.setup(sb, GPIO.IN)
GPIO.setup(km1, GPIO.OUT)

# Funciones de marcha y parada
def marcha(pm):

    global en_marcha
    global continuar
    en_marcha = True
    continuar = True
    print(en_marcha)
    
def parada(pp):

    global en_marcha
    en_marcha = False
    print(en_marcha)

# Interrupciones
GPIO.add_event_detect(pm, GPIO.RISING, callback = marcha)
GPIO.add_event_detect(pp, GPIO.RISING, callback = parada)

try:
    
    while 1:
        
        print(en_marcha)
        # Si el sistema esta en marcha y no se ha llegado al tope de agua, seguimos llenando
        if (en_marcha == True) and (GPIO.input(sa) == False):
    
            while 1:
            
                GPIO.output(km1, en_marcha)
                
                # Seguira hasta que llenemos el tanque
                if GPIO.input(sa):
                    
                    break

        # Si esta en marcha y se ha llenado, lo vaciamos
        elif (en_marcha == True) and (GPIO.input(sa) == True):
            
            continuar = False

            while 1:
                
                GPIO.output(km1, False)
                # Delay demostrativoS
                time.sleep(3)
                
                # Si hemos llegado a un nivel critico de agua, debemos volver a llenar el tanque
                if (GPIO.input(sb) == False) or (continuar == True):
                    
                    continuar = False
                    break
        
except KeyboardInterrupt: #If CTRL+C is pressed, exit cleanly:
    
    GPIO.cleanup()
```
