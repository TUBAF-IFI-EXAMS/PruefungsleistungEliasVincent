Zunächst unsere grundlegende Projektidee:

Wir wollen im Kontext des Machine Learnings und mittels Unity zur graphischen Darstellung eine KI erstellen, welche in der Lage sein soll, ein von uns erstelltes Spiel zu absolvieren. Dazu werden wir das ML.Net Framework nutzen, um effizient Daten aus dem Spiel auswerten zu können. 

Thema des Spiels: Der (KI-gesteuerte) Spieler befindet sich in einer Umgebung, welche mit zufällig generierten Hindernissen versehen ist. Seine Aufgabe ist es, einen vorgegebenen Zielpunkt in kürzester Zeit zu erreichen. 

Unser Aufgabenstellung umfasst im groben folgende Klassen:
- Gamemanager (Übernimmt jegliche Hintergrundoperationen des Spiels)
- PlayerHandler (Übersetzt Eingabewerte in die Bewegung des Spielerobjekts)
- Scoringclass (Bewertet die Handlung der KI bzw. des Spielers)
- Storeclass (Serialisiert die Daten der Scoringclass)
- Trainerclass (Lädt und nutzt Daten zum Erlernen des Verhaltens)
- UIManager