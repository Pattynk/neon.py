Juego neon.py <br>
Clase: graficación
28 de octubre del 2025
Irma Patricia Rivera León 
Sistemas Computacionales 5SS
Este documento recopila la información solicitada y da solución al ejercicio.


Ejercicio: 

Se manda el código de un juego de plataformas en donde los objetos en pantalla son rectángulos

Explicación de los rectángulos: se generan de acuerdo a las funciones de personaje (sprite type) y
colores correspondientes, en donde azul son plataformas con colisión, rojo es enemigo y verde es
el personaje principal o jugador. Las plataformas son objetos fijos en la pantalla por medio de 
coordenadas x,y, así como las dimensiones que deberían de tener, los enemigos cuentan con esta 
misma propiedad, sin embargo, ellos tienen la capacidad de moverse de manera cíclica a traves
del eje X al colisionar con los bordes (edges) de la pantalla, por último tenemos al jugador,
quien es un cuadrito que tiene mecánicas de movimiento lateral y salto con eventos de tipo 
(key_pressed) por medio de las flechas derecha e izquierda y del espacio del teclado.
_______________________________________________________________________________
# Al final del documento se anexa el código base por si usted gusta intentarlo.
_______________________________________________________________________________

Reto:

Dentro del código no vienen los rectángulos que servirán para dar "paredes" al juego,
en un descuido, el personaje caerá del escenario debido a su simulación de gravedad, 
por lo que el programador debe de establecer dichos rectángulos para incluirlos en
la declaración de plataformas y que así, nuestro jugador permanezca en zona segura.
_______________________________________________________________________________
# Dentro del otro archivo llamado neon.py, viene todo el juego ya terminado.
_______________________________________________________________________________


Solución: 

En primer lugar, se debe de tomar en cuenta el tamaño de la pantalla que este juego
genera al ser ejecutado, por lo que se rescata la línea donde lo menciona con:

# ANCHO, ALTO = 900, 500

Una vez establecido esto, será más fácil saber en donde ubicar estas paredes,
el siguiente paso fue revisar la manera en la que los rectángulos son dibujados
en pygames, por lo que recordemos que en la documentación se cita que es primero
la coordenada en (x,y) por medio de números, donde (0,0) corresponde al inicio de
la pantalla en la parte superior izquierda, y después de esto se establecen las 
dimensiones del rectángulo, de otra manera, si se juega solo con dos valores en x,y,
entonces se estaría estableciendo un solo punto en la pantalla, como el inicial, por 
ejemplo, o un punto de spawn o algo por el estilo, por lo que en realidad, se verá
como una serie de 4 valores de la siguiente manera (0,0,0,0).

# Inicia en (0,0) dimensiones de (10x460) según las dimensiones totales de la pantalla
    pygame.Rect(0, 0, 10, 460),   
# De aquí una vez creado el primero, ya solo faltaría con poner lo mismo del otro lado 
# de la pantalla, por lo que su coordenada en X se desplaza a la orilla cerca del 900
    pygame.Rect(890, 0, 10, 460), 



ANEXOS:
____________________________________________
# Se anexa el código inicial de la práctica
____________________________________________

import pygame
import sys

# Inicialización
pygame.init()
ANCHO, ALTO = 900, 500
pantalla = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Neon Platformer")

# Colores neón
NEON_RED = (255, 50, 50)
NEON_BLUE = (50, 200, 255)
NEON_GREEN = (0, 255, 128)
NEON_PURPLE = (200, 0, 255)
FONDO = (0, 0, 0)

clock = pygame.time.Clock()
fuente = pygame.font.SysFont("consolas", 22)

# Jugador
jugador = pygame.Rect(100, 400, 30, 40)
vel_y = 0
en_suelo = False
vel_x = 0

# Mundo / plataformas
plataformas = [
    pygame.Rect(0, 460, 900, 40),   # suelo
    pygame.Rect(200, 380, 120, 10),
    pygame.Rect(400, 300, 120, 10),
    pygame.Rect(650, 340, 150, 10),
]

# Enemigos
enemigos = [
    pygame.Rect(500, 430, 30, 30),
    pygame.Rect(700, 310, 30, 30)
]

# Movimiento enemigo
dir_enemigo = [1, -1]

# Función de dibujo neón
def dibujar_neon(rect, color, ancho=2):
    for i in range(5, 0, -1):
        pygame.draw.rect(pantalla, color, rect.inflate(i, i), ancho)

# Bucle principal
puntos = 0
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    # Movimiento del jugador
    teclas = pygame.key.get_pressed()
    vel_x = 0
    if teclas[pygame.K_LEFT]:
        vel_x = -5
    if teclas[pygame.K_RIGHT]:
        vel_x = 5
    if teclas[pygame.K_SPACE] and en_suelo:
        vel_y = -15
        en_suelo = False

    # Gravedad
    vel_y += 1
    if vel_y > 10:
        vel_y = 10

    # Movimiento horizontal
    jugador.x += vel_x

    # Colisión horizontal
    for p in plataformas:
        if jugador.colliderect(p):
            print("Colicion con horizontal")
            if vel_x > 0:
                jugador.right = p.left
            if vel_x < 0:
                jugador.left = p.right

    # Movimiento vertical
    jugador.y += vel_y

    # Colisión vertical
    en_suelo = False
    for p in plataformas:
        if jugador.colliderect(p):
            print("vertical")
            if vel_y > 0:
                jugador.bottom = p.top
                en_suelo = True
                vel_y = 0
            elif vel_y < 0:
                jugador.top = p.bottom
                vel_y = 0

    # Movimiento enemigo simple
    for i, e in enumerate(enemigos):
        e.x += dir_enemigo[i] * 2
        if e.left < 0 or e.right > ANCHO:
            dir_enemigo[i] *= -1

        # Colisión jugador-enemigo
        if jugador.colliderect(e):
            pygame.quit()
            sys.exit()

    # Fondo
    pantalla.fill(FONDO)

    # Dibujar plataformas y suelo
    for p in plataformas:
        dibujar_neon(p, NEON_BLUE)

    # Dibujar jugador
    dibujar_neon(jugador, NEON_GREEN)

    # Dibujar enemigos
    for e in enemigos:
        dibujar_neon(e, NEON_RED)

    # Puntos (incrementan con el tiempo)
    puntos += 1
    texto = fuente.render(f"Puntos: {puntos // 10}", True, NEON_PURPLE)
    pantalla.blit(texto, (10, 10))

    pygame.display.flip()
    clock.tick(60)

