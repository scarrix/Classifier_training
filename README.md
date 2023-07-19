# Classifier_training
Il repository GitHub in questione addestra un calcolatore a cascata che, tramite un dataset di immagini, permette di effettuare il detection dei piedi di robot NAO.

# Gestione dei positivi
All’interno di pos_top e neg_top mettiamo, rispettivamente, le immagini positive e negative.
Il sorgente createpos.cpp invece verrà usato per generare un file di listing delle immagini positive, denominato positive.txt, in un formato adatto a OpenCV. 
Compiliamo il sorgente tramite il comando 
```
g++ createpos.cpp -o createpos -lws2_32
```
otterremo nella stessa cartella createpos.exe. Eseguiamo da terminale, nella radice del progetto, il comando 
```
createpos path\to\pos_top
```
per creare il file positive.txt. Settando appropriatamente OpenCV e le variabili d’ambiente, sarà possibile utilizzare l’applicazione opencv_createsamples per produrre un set di dati di campioni positivi in formato .vec, supportato dall’applicazione di training opencv_traincascade. 
L'output ottenuto da terminale, dall’esecuzione del comando 
```
opencv_createsamples -info positives.txt -w 24 -h 24 -vec pos.vec -num 760
```
è il file pos.vec. 
I parametri -w e -h sono le dimensioni del bounding box per il cropping delle immagini positive, -vec è il nome del file vettoriale creato e -num indica il numero di immagini positive usate.

# Gestione dei negativi
Infine si crea un file txt di listing anche per le immagini negative eseguendo da terminale il comando 
```
dir /b/s .\neg_top\*.jpg > negatives.txt
```
nella radice del progetto, e si passa alla fase di training del classificatore.

# Addestramento
L'addestramento del classificatore viene eseguito tramite il comando 

```
opencv_traincascade -data classifier -vec pos.vec -bg negatives.txt -numStages 20 -minHitRate 0.999 -maxFalseAlarmRate 0.5 -numPos 176 -numNeg 356 -w 24 -h 24 -mode ALL -precalcValBufSize 256 -precalcIdxBufSize 256 -acceptanceRatioBreakValue 10e-5 -nonsym -baseFormatSave -featureType LBP
```

Tra i parametri più importanti, oltre quelli già introdotti, ci sono -data che è la cartella nella quale dovranno essere salvati gli xml di training (quindi la cartella classifier); -bg è il parametro che indica il file di listing delle immagini negative; -numStage indica il numero di stadi di allenamento; -nonsym viene usato nel caso in cui il modello preso in considerazione non è simmetrico in tutte le viste possibili. 
