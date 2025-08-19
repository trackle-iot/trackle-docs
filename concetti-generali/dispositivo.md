# Dispositivo

Un dispositivo è un hardware basato su MCU (ad es. NodeMCU, Arduino, Prodino, Raspberry Pi, ecc.) oppure un prodotto fisico finito come un termostato o una VMC.

## Autenticazione

Ogni dispositivo su Trackle ha:

* un **deviceID** di 12 byte che lo identifica in maniera univoca che può essere generato dal Cloud oppure deciso in fase di produzione
* una coppia di chiavi pubblica / privata in formato **RPK** (_Raw Public Key_) per l'autenticazione del dispositivo
* la **chiave pubblica** del Cloud per l'autenticazione del server cloud

## Proprietario

Quando connetti un dispositivo al Cloud per la prima volta, questo non avrà alcun **proprietario** quindi nessuno, eccetto nel caso di un dispositivo associato ad un **Prodotto**,  avrà il permesso di controllarlo. Per poter associare un Dispositivo ad un account e poterlo quindi monitorare e controllare è necessario effettuare la **procedura di claim**, direttamente dalla Console o tramite un codice di claim. Dopo questa operazione solo quell'account avrà il permesso di controllare il dispositivo.

## Permessi

Il permesso di accedere alle informazioni di un dispositivo è concesso al proprietario e, nel caso di un dispositivo associato ad un **Prodotto,** agli account autorizzati a gestirlo. Possono accedere al dispositivo anche ad App di terze parti tramite autenticazione **OAuth**.
