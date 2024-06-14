# LED Strip

---

## Hardware

* [ESP-WROOM-32](https://www.dzduino.com/Blog%20Dzduino/ESP32-WROOM-32) à
programmer sur Arduino IDE en sélectionnant *ESP32-WROOM-DA Module*

* Bande de LED type GRB

* **ATTENTION** l'ESP32 et la bande de LED doivent être sur la même alimentation

>![](https://private-user-images.githubusercontent.com/165054887/327752291-b09e4d5f-e7b7-427a-b346-25d7127fa625.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTgzNjg5MzQsIm5iZiI6MTcxODM2ODYzNCwicGF0aCI6Ii8xNjUwNTQ4ODcvMzI3NzUyMjkxLWIwOWU0ZDVmLWU3YjctNDI3YS1iMzQ2LTI1ZDcxMjdmYTYyNS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjE0JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYxNFQxMjM3MTRaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zOTMxYThmZTcyYzkxNzBhYTg2ZDI4OTg3YmU4OTNlNmIyMGNmYmUyZTMzODU5YWE3ZWQ1MmY4NGM0MTVhMmY5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.-PU7_Mq48kBoJLmbEonBNRFvaPjVXDXUZICA5LxX_Zk)
>
>*Test réalisés avec un condensateur de 1000µF et une résistance de 330Ω*

* CAN Transceiver MCP2561

>![](https://private-user-images.githubusercontent.com/165054887/327752294-5873006a-e3a5-4eda-a57a-e8a4a1f7ddb5.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTgzNjkwMDUsIm5iZiI6MTcxODM2ODcwNSwicGF0aCI6Ii8xNjUwNTQ4ODcvMzI3NzUyMjk0LTU4NzMwMDZhLWUzYTUtNGVkYS1hNTdhLWU4YTRhMWY3ZGRiNS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjE0JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYxNFQxMjM4MjVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT02ODA3ZWI5ODI1N2I4ZTZhOTY0M2Q1YjMzZjMyMjkxZjg0NjQ0ODU1NTEzNzc0YzI2YzdhZTAxOTk4ZmU0NzIzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.nEibg9dduYASWZruJXR8DaPOUI_j9wIl3HvYsQF6VtI)

<br>

| ESP32  |  LED Strip | MCP2561  |
|---|---|---|
|  VIN |  +5V  | /  |
|  GND | GND  | /  |
| 26  | DIN  | /  |
| 22  | /  |  TXD |
| 23  | /  | RXD  |

---

## Trame CAN

Liste des trames de chaque mode

#### Mode configuration

Le nombre de LED est un entier sur 16 bits, les 4 octets suivants sont les numéros de la première LED de chaque côté du robot (*exemple, si la bande de LED commence à l'arrière droite du robot et que chaque côté de robot contient 20 LED on aura les valeurs 0, 20, 40,60*)

| Octet 0 (Mode) |  Octet 1 | Octet 2  |Octet 3|Octet 4|Octet 5|Octet 6|
|---|---|---|---|---|---|---|
| 0 |Nombre de LED <15:8> |Nombre de LED <7:0>|Coordonnées début arrière|Coordonnées début gauche|Coordonnées début avant|Coordonnées début droite|

#### Mode Chargement

Ce mode est lancé dès que l'ESP32 est alimentée jusqu'à ce qu'une nouvelle trame soit reçue, on il n'a que la luminosité pour paramètre

| Octet 0 (Mode) |  Octet 1 |
|---|---|
| 1 | Luminosité |

#### Mode Coordonnées

Ce mode allume les LED selon les coordonnées et les valeurs R, G et B dans la trame, les LED en dehors des coordonnées gardent leur couleur précédente.

>**Le paramètre luminosité modifie toute la bande de LED**

| Octet 0 (Mode) |  Octet 1 | Octet 2  |Octet 3|Octet 4|Octet 5|Octet 6|Octet 7|
|---|---|---|---|---|---|---|---|
| 2 | Coordonnées de début |Coordonnées de fin|R|G|B|Luminosité|Reset|

#### Mode Arc-En-Ciel


| Octet 0 (Mode) |  Octet 1 |
|---|---|
| 3 | Luminosité |

#### Mode Couleur Uni

Toute la bande de LED s'allume à la couleur et la luminosité demandée

| Octet 0 (Mode) |  Octet 1 | Octet 2  |Octet 3|Octet 4|
|---|---|---|---|---|
| 4 |R|G|B|Luminosité|

#### Mode 5

Même paramètres qu'avant mais une LED sur deux s'allume

| Octet 0 (Mode) |  Octet 1 | Octet 2  |Octet 3|Octet 4|
|---|---|---|---|---|
| 5 |R|G|B|Luminosité|

#### Mode Clignontant

Ce mode utilise les coordonnées arrière, gauche, devant et droite afin d'afficher des clignotants sur la partie arrière. La direction du clignotant est choisi avec l'octet direction, si il est égal à 1 le clignotant va vers la droite, si il est égal à 0 le clignotant va à gauche. On a un également un paramètre de luminosité comme avant

| Octet 0 (Mode) |  Octet 1 | Octet 2  |
|---|---|---|
| 6 |Direction |Luminosité|

---

## Software



###### Bande de LED

Déclaration de la bande de LED , *strip*

`Adafruit_NeoPixel strip(Strip_Length, LED_PIN, NEO_GRB);`

Tableau contenant les données de couleurs, *Strip_Array* changer la taille du
tableau si le nombre de LED dépasse 500

`rgb Strip_Array[500];`

Ligne de c

`strip.updateLength(Strip_Length);`

###### Bus CAN

Filtre de réception

`CAN.filter(0x7FE);`

Fonction d'IT sur réception de trame

`CAN.onReceive(onReceive);`

###### Fonctionnement

Dès qu'on reçoit une trame dont l'ID correspond on va dans onReceive pour récupérer les valeurs dans la trame et les mettre dans les variables utilisées pour l'affichage

Pour l'affichage on utilise la fonction `void LED_Strip (uint8_t mode)`, elle a pour argument le mode récupéré dans la trame et va envoyer les données sur la bande de LED
