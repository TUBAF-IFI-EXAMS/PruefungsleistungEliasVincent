## Übersicht

Das Skript `FeatureBuilder.cs` beinhaltet folgende Klassen:

* `AddressLookupTable` --> Übersetzt Zustandsaddressen in den Index der Belohnungs- und Q-Matrix.
* `FeatureBuilder` --> Verkettete Liste aus `AIFeatures`. Der `FeatureBuilder` wird genutzt um die Belohnungsmatrix zu generieren und das `AIFeatureInterface` zu erstellen.
* `InputRange` --> Definiert wie ein `AIFeature` eine double Eingabe übersetzt.
* `AIFeature` --> Verknüpft die möglichen Aktionen mit einer Eingabequelle.
* `AIFeatureInterface` --> Wird genutzt um die KI mit Eingabedaten zu versorgen. Es beinhaltet alle `AIFeatures` der KI.
* `AIAction` --> Definiert eine Ausgabe der KI. `AIAction` wird nicht vom Benutzer instanziiert sondern indirekt durch `AIActionInterface`. 
* `AIActionInterface` --> Sammelt alle Ausgabemöglichkeiten der KI.

Außerdem gibt es die statische Klasse `FeatureBuilderTools` welche Methoden zum einfacheren Debuggen bereitstellt.

## Instanziierung eines Agents
### 1. AIActionInterface
Zu Beginn werden die möglichen Ausgaben (Actions) des Agents definiert. Dafür instanziieren wir ein Objekt vom Typ `AIActionInterface`:

    AIActionInterface AInterface=new AIActionInterface();
        AInterface.addAction("W");
        AInterface.addAction("A");
        AInterface.addAction("S");
        AInterface.addAction("D");

Die Methode `addAction()` fügt der KI eine weitere Ausgabemöglichkeit hinzu. Es sollten mindestens zwei Actions hinzugefügt werden.

### 2. AIFeature
Als nächstes müssen die Features definiert werden. Das sind die Eingaben der KI. Für jede Informationsquelle muss ein `AIFeature` erstellt werden:

    AIFeature WallFront=new AIFeature(
            new double[4][]{//W A S D
                new double[]{-100,0,10,0},//0
                new double[]{-80,0,8,0},//1
                new double[]{-40,0,4,0},//2
                new double[]{1,1,1,1}//3
            },
            new InputRange[]{
                new InputRange(0,50),
                new InputRange(1,40,50),
                new InputRange(2,30,40),
                new InputRange(3,0,30),
            },
            AInterface, //connectedInterface
            "WallFront"
        );

Das erste Argument des Konstruktors ist ein zweidimmensionaler Double Array. Dieser bestimmt die Belohnungen die die KI während des Lernens für eine ausgeführte Action bekommt. Dabei sind die Spalten des Arrays die jeweilige Action (in der Reinfolge wie sie dem `AIActionInterface` hinzugefügt wurden) und die Zeilen die Stufe in dem sich die Eingangsgröße befindet. (Siehe Kommentare). Das dritte Argument ist das `AIActionInterface` der KI. Als viertes optionales Argument kann dem Feature noch eine Bezeichnung übergeben werden.

Die instanziierung der `AIFeatures` erfolgt in unserem Beispiel vier mal für die Features:
* `WallFront`
* `WallBack`
* `WallLeft`
* `WallRight`

### 3. FeatureBuilder
Aus den einzelnen Features muss nun eine Matrix mit allen möglichen Kombinationen der Features und dessen stufen erstellt werden. Dazu wird der `FeatureBuilder` verwendet. Der `FeatureBuilder` basiert auf dem Prinzip der Verkettung. Man kann ihn auf zwei Arten instanziieren:

    FeatureBuilder fa0=new FeatureBuilder(WallFront);
    FeatureBuilder fa1=new FeatureBuilder(WallBack,fa0);
    FeatureBuilder fa2=new FeatureBuilder(WallLeft,fa1);
    FeatureBuilder fa3=new FeatureBuilder(WallRight,fa2);
 
oder

    FeatureBuilder fa3=new FeatureBuilder(WallRight,new FeatureBuilder(WallLeft,new FeatureBuilder(WallBack,new FeatureBuilder(WallFront))));

Bei letzterer Schreibweise muss die Reinfolge der Features umgekehrt werden.

### 4. RewardMatrix
Nun bilden wir die Belohnungsmatrix vom Typ `RewardMatrix`. Dem Konstruktor übergeben wir den `FeatureBuilder` der alle `AIFeatures` enthält.

    RewardMatrix rewMatrix=new RewardMatrix(fa3);

Damit die Matrix tatsächlich auch berechnet wird rufen wir `rewMatrix.generateMatrix();` auf.

### 5. AIFeatureInterface
Eingaben, also Feature Werte, werden der dem Agent über ein `AIFeatureInterface` übergeben. Dieses wird manuell instanziiert und dem `FeatureBuilder`übergeben. Die Rückgabe ist das fertige `AIFeatureInterface`.

    AIFeatureInterface fi=new AIFeatureInterface();
    fi=fa3.getFeatureInterface(fi);

### 6. QAgent
Als letzten Schritt instanziieren wir die eigentliche KI vom Typ `QAgent`. Als Argumente übergeben wir die Belohnungsmatrix (`RewardMatrix`) und das `AIActionInterface`.

    QAgent ai=new QAgent(rewMatrix,AInterface);

## Trainieren des Agents
Nachdem der Agent korrekt instanziiert wurde, kann des `AIFeatureInterface` mit Daten gefüllt werden.

    fi[0].CurrentRawInput=35;
    fi[1].CurrentRawInput=30;
    fi[2].CurrentRawInput=1000;
    fi[3].CurrentRawInput=80;

Der Agent ist zu diesen Zeitpunkt noch komplett unerfahren und wird anschließend eine rein zufällige Entscheidung treffen. Entscheidungen werden so abgefragt:

    AIAction prediction=ai.Predict(fi);

Diese Vorhersage sollte nun am Realobjekt angewendet werden. Ist das geschehen muss `AIFeatureInterface` mit den aktuellen Daten gefüllt werden. Anschließend wird 

    ai.Reward(fi);

aufgerufen. `ai.Reward(AIFeatureInterface _input)` wendet den Q-Algorithmus auf die QMatrix des Agents an. Dabei beachtet er den letzten Zustand, die vorhergesagte Action und den jetzigen Zustand. Anhand der Belohnungsmatrix wird nun die entsprechende Zelle der Matrix erneuert.

Nach `ai.Reward(fi);` kann sofort `ai.Predict(fi);` aufgerufen werden. Dieser Prozess sollte solange wiederholt werden bis der Agent zufriedenstellende Ergebnisse liefert.

## Nutzen des Agents
Ist der Agent trainiert dann kann auf `ai.Reward(fi);` verzichtet werden. 

**Achtung: zum jetzigen Zeitpunkt muss außerdem `ai.setParams(0.5,0.5,1);` aufgerufen werden um zufällige Entscheidungen des Agents zu deaktivieren.** 



