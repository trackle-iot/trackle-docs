# Prodotto

I dispositivi possono essere parte di un **Prodotto**. Un prodotto identifica un gruppo di dispositivi con lo stesso hardware e le stesse funzionalità. Immagina dei termostati intelligenti connessi: svolgono tutti una funzione simile e possiamo presumere che abbiano lo stesso hardware, firmware, GPIO, ecc... per questo vengono identificati dal Cloud attraverso un **productID** comune. In questo modo se si vogliono fare modifiche al firmware di tutti i dispositivi si rilascia un aggiornamento OTA di prodotto, aggiornando così l'intera flotta.

Un prodotto appartiene ad un organizzazione ed ha un proprietario, colui che l'ha creato, che risulta l'amministratore di prodotto. Ogni prodotto può avere un **Team** di più persone che può accedere alle informazioni dei dispositivi limitando i permessi di accesso attraverso i ruoli.

