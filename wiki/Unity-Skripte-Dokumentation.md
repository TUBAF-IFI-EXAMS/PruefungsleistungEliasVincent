## Übersicht der Unity bezogenen Skripte

### GameManager.cs
Im Gamemanager werden die AIFeatures definiert sowie durch den Modellbuilder erfasst. Anschließend werden Reward Matrix und QAgent generiert.
Der Gamemanager verfügt über spielumfassende Funktionen, welche zur Regelung der Szene benötigt werden. Beispielsweise der Aufruf zum Respawnen des Spielers sowie die erneute Generierung aller Hindernisse.

### ObstacleGenerator.cs
Der ObstacleGenerator übernimmt die Aufgabe der Platzierung und Löschung aller Hindernisse. Er verfügt über eine Hindernisliste, welche zur Generierung durchlaufen wird. Damit die Szene nicht unpassierbar wird, löscht der ObstacleGenerator alle verbliebenen Hindernissen, bevor neue gespawnt werden.

### PlayerObject.cs
Das Spielerobjekt umfasst die Steuerung des Spielers sowie diverse Abstandsmessungen. Die Steuerung erfolgt durch einen Rigidbody. Dieser kontrolliert die Position des Spielers anhand einer physikalischer Simulation. Werden Trigger angesprochen, so erfolgt daraus eine vordefinierte Bewegung. Der KI werden aktuelle Parameter übergeben, wodurch sie ihre Voraussagungen treffen kann. Diese können wiederum als Trigger interpretiert werden sodass die KI den Spieler bewegt. Die Abstandmessungen erfolgen durch 8 verschiedene Raycasts, welche Collider auf dem "Obstacle" Layer erfassen und besagte aktuelle Parameter zurückgeben. 

### TargetObject.cs
Die Klasse des Zielobjekts dient zum Spawnen des Ziels an einer zufälligen Position sowie der Messung der Rundenzeiten. Ebenfalls wird beim Spawnen ein Event getriggert, welches dem UIManager hilft, die korrekte Anzahl der Runden zu visualisieren. 

### UIManager.cs
Der UIManager übernimmt die Darstellung der UI Elemente. 