# Limiti alle richieste

Le richieste alle API sono limitate approssimativamente a 10 al secondo verso `api.trackle.io` per ogni IP pubblico. Questo limite è il numero totale di chiamate da un IP pubblico e non dipendono dall'access token utilizzato o dall'endpoint della richiesta.

## **Attenzione a monitorare i cambiamenti delle variabili** <a href="#beware-of-monitoring-variables-for-change" id="beware-of-monitoring-variables-for-change"></a>

Una situazione che può causare problemi è il continuo monitoraggio delle variabili in attesa di cambiamenti. Se il software sta in polling ogni secondo su una variabile per un solo dispositivo non ci sono problemi, ma se vengono monitorati molti dispositivi, si può facilmente superare il limite.

E' molto più efficiente chiamare `Trackle.publish()` dal dispositivo quando un valore cambia.

## **Gestisci gli errori nel modo corretto** <a href="#make-sure-you-handle-error-conditions-properly" id="make-sure-you-handle-error-conditions-properly"></a>

Se il software ottiene un errore `401 (Unauthorized)` significa che l'access\_token è sicuramente scaduto. Continuare a riprovare con la stessa richiesta non cambia il risultato.

Se si ottiene un errore `429 (Too many requests)` significa che si è già superato il limite delle richieste, quindi continuare con le richieste non aiuta.&#x20;

In generale in caso di errore si può considerare di aspettare un pò prima di eseguire la richiesta successiva.
