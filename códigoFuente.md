from machine import Pin, PWM
import neopixel
import utime

# Configuración del servo motor en el pin 13
servo = PWM(Pin(13), freq=50)  # PWM en el pin 13 con una frecuencia de 50 Hz

# Configuración de la tira de LED (Neopixel) en el pin 26 con 16 LEDs
num_leds = 16
np = neopixel.NeoPixel(Pin(26), num_leds)

def map_angle_to_duty(angle):
    """
    Convierte un ángulo (0° a 180°) en el valor correspondiente de "duty".
    Ajustado para el método 'duty' en MicroPython v1.19.
    """
    return int((angle - 0) * (125 - 25) / (180 - 0) + 25)

def move_servo_and_led():
    """
    Bucle continuo para mover el servo y cambiar el color de todos los LEDs.
    - Verde: al mover de 0° a 180° (y LED blanco avanza de 0 a 180)
    - Rojo: al mover de 180° a 0° (y LED blanco avanza de 180 a 0)
    """
    while True:
        # Mover de 0° a 180° (LEDs en verde y LED blanco avanza de 0 a 180)
        for angle in range(0, 181, 15):  # Recorre de 0° a 180° en pasos de 15°
            duty = map_angle_to_duty(angle)  # Convierte el ángulo a "duty cycle"
            servo.duty(duty)  # Mueve el servo al ángulo correspondiente

            # Mantener todos los LEDs en verde
            np.fill((0, 255, 0))  # Todos los LEDs en verde
            np.write()  # Actualiza la tira de LED

            # Apagar todos los LEDs y encender solo el LED blanco en la posición correspondiente
            led_position = int(angle / 180 * (num_leds - 1))  # Calcula la posición del LED en la tira
            np[led_position] = (255, 255, 255)  # LED blanco
            np.write()  # Actualiza la tira de LED

            print("Ángulo:", angle, "- Color: Verde - LED Blanco en posición:", led_position)
            utime.sleep(0.2)  # Pausa para permitir el movimiento visible

        # Mover de 180° a 0° (LEDs en rojo y LED blanco avanza de 180 a 0)
        for angle in range(180, -1, -15):
            duty = map_angle_to_duty(angle)
            servo.duty(duty)

            # Mantener todos los LEDs en rojo
            np.fill((255, 0, 0))  # Todos los LEDs en rojo
            np.write()  # Actualiza la tira de LED

            # Apagar todos los LEDs y encender solo el LED blanco en la posición correspondiente
            led_position = int(angle / 180 * (num_leds - 1))  # Calcula la posición del LED en la tira
            np[led_position] = (255, 255, 255)  # LED blanco
            np.write()  # Actualiza la tira de LED

            print("Ángulo:", angle, "- Color: Rojo - LED Blanco en posición:", led_position)
            utime.sleep(0.2)  # Pausa para permitir el movimiento visible

# Ejecutar el programa principal
move_servo_and_led()

