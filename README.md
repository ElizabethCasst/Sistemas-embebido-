#!/usr/bin/env python3
import RPi.GPIO as GPIO
import time

# Pines de los LEDs
PINS = [17, 18, 27, 22, 23]

def configurar_pwm(pines, frecuencia=200):
    pwms = []
    for pin in pines:
        GPIO.setup(pin, GPIO.OUT)
        pwm = GPIO.PWM(pin, frecuencia)
        pwm.start(0)
        pwms.append(pwm)
    return pwms

try:
    GPIO.setmode(GPIO.BCM)
    GPIO.setwarnings(False)

    pwms = configurar_pwm(PINS)

    print("Efecto PWM suave entre LEDs iniciado")
    print("Presiona Ctrl+C para salir\n")

    while True:
        for i in range(len(pwms)):
            siguiente = (i + 1) % len(pwms)

            # Transición suave entre LED actual y el siguiente
            for duty in range(0, 101, 5):
                pwms[i].ChangeDutyCycle(100 - duty)   # baja
                pwms[siguiente].ChangeDutyCycle(duty) # sube
                time.sleep(0.03)

except KeyboardInterrupt:
    print("\nPrograma detenido por el usuario")

finally:
    for pwm in pwms:
        pwm.stop()
    GPIO.cleanup()
    print("GPIO limpiado. Programa finalizado.")
