---
lab:
  title: Ausführen eines Trainingsskripts als Befehlsauftrag in Azure Machine Learning
---

# Ausführen eines Trainingsskripts als Befehlsauftrag in Azure Machine Learning

Ein Notebook eignet sich ideal für Experimente und Entwicklung. Sobald Sie ein Machine Learning-Modell entwickelt haben und es für die Produktion bereit ist, sollten Sie es mit einem Skript trainieren. Sie können ein Skript als Befehlsauftrag ausführen.

In dieser Übung testen Sie ein Skript und führen es dann als Befehlsauftrag aus.

## Vorbereitung

Sie benötigen ein [Azure-Abonnement](https://azure.microsoft.com/free?azure-portal=true), in dem Sie Administratorzugriff besitzen.

## Bereitstellung eines Azure Machine Learning-Arbeitsbereichs

Ein Azure Machine Learning-*Arbeitsbereich* ist eine Zentrale zum Verwalten aller Daten- und anderen Ressourcen, die Sie zum Trainieren und Verwalten Ihrer Modelle benötigen. Sie können mit dem Azure Machine Learning-Arbeitsbereich über Studio, das Python SDK und die Azure CLI interagieren.

Mithilfe der Azure CLI stellen Sie den Arbeitsbereich und die erforderliche Compute-Instanz bereit, und mithilfe des Python SDK führen Sie einen Befehlsauftrag aus.

### Erstellen des Arbeitsbereichs und der Computeressourcen

Zum Erstellen des Azure Machine Learning-Arbeitsbereichs, einer Compute-Instanz und eines Computeclusters verwenden Sie die Azure CLI. Alle erforderlichen Befehle sind in einem Shellskript gruppiert, das Sie ausführen können.

1. Öffnen Sie in einem Browser unter `https://portal.azure.com/` das Azure-Portal, und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Wählen Sie oben auf der Seite rechts neben dem Suchfeld die Schaltfläche \[>_] (*Cloud Shell*) aus. Dadurch wird am unteren Rand des Portals ein Cloud Shell-Bereich geöffnet.
1. Wählen Sie bei Aufforderung **Bash** aus. Wenn Sie die Cloud Shell erstmals öffnen, werden Sie zur Wahl der gewünschten Shell (*Bash* oder *PowerShell*) aufgefordert.
1. Stellen Sie sicher, dass das gewünschte Abonnement angegeben ist, und wählen Sie **Speicher erstellen** aus, wenn Sie aufgefordert werden, Speicher für Ihre Cloud Shell zu erstellen. Warten Sie, bis der Speicher erstellt wurde.
1. Geben Sie im Terminal die folgenden Befehle ein, um dieses Repository zu klonen:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Kopieren Sie mit `SHIFT + INSERT` Ihren Code in die Cloud Shell.

1. Nachdem das Repository geklont wurde, geben Sie die folgenden Befehle ein, um in den Ordner für dieses Lab zu wechseln. Führen Sie das darin enthaltene Skript **setup.sh** aus:
    
    ```azurecli
    cd azure-ml-labs/Labs/08
    ./setup.sh
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Erweiterungen nicht installiert wurden.

1. Warten Sie, bis das Skript abgeschlossen ist. Dies dauert in der Regel etwa 5–10 Minuten.

## Klonen der Labmaterialien

Wenn Sie den Arbeitsbereich und die erforderlichen Computeressourcen erstellt haben, können Sie Azure Machine Learning Studio öffnen und die Labmaterialien in den Arbeitsbereich klonen.

1. Navigieren Sie im Azure-Portal zum Azure Machine Learning-Arbeitsbereich mit dem Namen **mlw-dp100-...** .
1. Wählen Sie den Azure Machine Learning-Arbeitsbereich und dann auf der Seite **Übersicht** die Option **Studio starten** aus. In Ihrem Browser wird eine weitere Registerkarte geöffnet, auf der Azure Machine Learning Studio geöffnet wird.
1. Schließen Sie alle Popupelemente, die in Studio angezeigt werden.
1. Navigieren Sie innerhalb von Azure Machine Learning Studio zur Seite **Compute**, und überprüfen Sie, ob die Compute-Instanz und der Cluster vorhanden sind, die Sie im vorherigen Abschnitt erstellt haben. Die Compute-Instanz sollte ausgeführt werden, der Cluster sollte sich mit 0 ausgeführten Knoten im Leerlauf befinden.
1. Navigieren Sie auf der Registerkarte **Compute-Instanzen** zu Ihrer Compute-Instanz, und wählen Sie die Anwendung **Terminal** aus.
1. Installieren Sie im Terminal das Python SDK in der Compute-Instanz, indem Sie die folgenden Befehle ausführen:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Pakete nicht gefunden und deinstalliert werden konnten.

1. Führen Sie den folgenden Befehl aus, um ein Git-Repository mit Notebooks, Daten und anderen Dateien in Ihrem Arbeitsbereich zu klonen:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Wenn der Befehl abgeschlossen ist, klicken Sie im Bereich **Dateien** auf **&#8635;** , um die Ansicht zu aktualisieren und sicherzustellen, dass der neue Ordner **Users/*Ihr-Benutzername*/azure-ml-labs** erstellt wurde.

## Konvertieren eines Notebooks in ein Skript

Die Verwendung eines an eine Compute-Instanz angefügten Notebooks ist ideal für Experimente und Entwicklung, da Sie damit den von Ihnen geschriebenen Code sofort ausführen und seine Ausgabe überprüfen können. Um von der Entwicklungs- auf die Produktionsumgebung umzustellen, sollten Sie Skripts verwenden. Als ersten Schritt können Sie in Azure Machine Learning Studio Ihr Notebook in ein Skript konvertieren.

1. Öffnen Sie das Notebook **Labs/08/src/Train classification model.ipynb**.

    > Wählen Sie **Authentifizieren** aus, und führen Sie die erforderlichen Schritte aus, wenn eine Benachrichtigung angezeigt wird, in der Sie zur Authentifizierung aufgefordert werden.

1. Stellen Sie sicher, dass das Notebook den Kernel **Python 3.8 – AzureML** verwendet.
1. Führen Sie alle Zellen aus, um den Code zu untersuchen und ein Modell zu trainieren.
1. Wählen Sie oben im Notebook das Symbol &#9776; aus, um das Menü**Notebook** anzuzeigen.
1. Klappen Sie **Exportieren als** auf, und wählen Sie **Python (.py)** aus, um das Notebook in ein Python-Skript zu konvertieren.
1. Geben Sie der neuen Datei den Namen `train-classification-model`.
1. Sobald die neue Datei erstellt wurde, sollte das Skript automatisch geöffnet werden. Erkunden Sie die Datei. Sie stellen fest, dass sie denselben Code wie das Notebook enthält.
1. Wählen Sie oben im Notebook das Symbol &#9655;&#9655; aus, um **das Skript im Terminal zu speichern und auszuführen**.
1. Das Skript wird vom Befehl **python train-classification-model.py** eingeleitet, und die Ausgabe sollte unterhalb des Befehls angezeigt werden.

## Testen eines Skripts mit dem Terminal

Nachdem Sie ein Notebook in ein Skript konvertiert haben, können Sie es weiter optimieren. Eine bewährte Methode beim Arbeiten mit Skripts ist die Verwendung von Funktionen. Wenn Ihr Skript aus Funktionen besteht, können Sie Ihren Code einfacher testen. Wenn Sie Funktionen verwenden, besteht Ihr Skript aus Codeblöcken, die jeweils eine bestimmte Aufgabe ausführen.

1. Öffnen Sie das Skript **Labs/08/src/train-model-parameters.py**, und erkunden Sie dessen Inhalt.
    Beachten Sie, dass es eine Hauptfunktion gibt, die vier weitere Funktionen enthält:

    - Lesen von Daten
    - Aufteilen von Daten
    - Trainieren des Modells
    - Bewerten eines Modells

    Nach der Hauptfunktion werden die einzelnen Funktionen definiert. Beachten Sie, wie jede Funktion die erwartete Ein- und Ausgabe definiert.

1. Wählen Sie oben im Notebook das Symbol &#9655;&#9655; aus, um **das Skript im Terminal zu speichern und auszuführen**. Nach **Daten werden gelesen...** sollte die Fehlermeldung angezeigt werden, dass die Daten nicht abgerufen werden konnten, da der Dateipfad ungültig war.

    Mithilfe von Skripts können Sie Ihren Code parametrisieren, um die Eingabedaten oder -parameter einfach zu ändern. In diesem Fall erwartet das Skript einen Eingabeparameter für den Datenpfad, den wir nicht angegeben haben. Sie finden die definierten und erwarteten Parameter am Ende des Skripts in der Funktion **parse_args()** .

    Zwei Eingabeparameter sind definiert:
    - **--training_data**, der eine Zeichenfolge erwartet.
    - **--reg_rate**, der eine Zahl erwartet, aber den Standardwert 0,01 hat.

    Um das Skript erfolgreich auszuführen, müssen Sie Werte für die Trainingsdatenparameter angeben. Dazu verweisen wir auf die Datei **diabetes.csv**, die sich im selben Ordner wie das Trainingsskript befindet.

1. Führen Sie folgenden Befehl im Terminal aus:

    ```
    python train-model-parameters.py --training_data diabetes.csv
    ```

Das Skript sollte erfolgreich ausgeführt werden, und als Ergebnis sollte die Ausgabe die Genauigkeit und AUC des trainierten Modells wiedergeben.

Das Testen des Skripts im Terminal ist ideal, um zu prüfen, ob das Skript wie erwartet funktioniert. Wenn ein Problem mit dem Code vorliegt, erhalten Sie im Terminal eine Fehlermeldung.

Bearbeiten Sie **optional** den Code, um einen Fehler zu erzwingen, und führen Sie den Befehl im Terminal erneut aus, um das Skript auszuführen. Entfernen Sie zum Beispiel die Zeile **import pandas as pd**, speichern Sie das Skript, und führen Sie es mit dem Eingabeparameter aus, um die Fehlermeldung zu überprüfen.

## Ausführen eines Skripts als Befehlsauftrag

Wenn Sie wissen, dass Ihr Skript funktioniert, können Sie es als Befehlsauftrag ausführen. Wenn Sie Ihr Skript als Befehlsauftrag ausführen, können Sie alle Ein- und Ausgaben des Skripts nachverfolgen.

1. Öffnen Sie das Notebook **Labs/08/Run script as command job.ipynb**.
1. Führen Sie alle Zellen im Notebook aus.
1. Navigieren Sie in Azure Machine Learning Studio zur Seite **Aufträge**.
1. Navigieren Sie zum Auftrag **diabetes-train-script**, um die Übersicht über den ausgeführten Befehlsauftrag zu erkunden.
1. Navigieren Sie zur Registerkarte **Code**. Alle Inhalte des Ordners **src**, der der Wert des Parameters **code** des Befehlsauftrags war, werden hierhin kopiert. Sie können das Trainingsskript überprüfen, das vom Befehlsauftrag ausgeführt wurde.
1. Navigieren Sie zur Registerkarte **Ausgaben und Protokolle**.
1. Öffnen Sie die Datei **std_log.txt**, und untersuchen Sie ihren Inhalt. Der Inhalt dieser Datei ist die Ausgabe des Befehls. Denken Sie daran, dass die gleiche Ausgabe im Terminal gezeigt wurde, als Sie das Skript dort getestet haben. Wenn der Auftrag aufgrund eines Skriptproblems nicht erfolgreich ist, wird die Fehlermeldung hier angezeigt.

Bearbeiten Sie **optional** den Code, um einen Fehler zu erzwingen, und verwenden Sie das Notebook, um den Befehlsauftrag erneut einzuleiten. Entfernen Sie beispielsweise die Zeile **import pandas as pd** aus dem Skript, und speichern Sie es. Oder bearbeiten Sie die Konfiguration des Befehlsauftrags, um die Fehlermeldungen zu erkunden, wenn mit der Auftragskonfiguration selbst statt mit dem Skript etwas nicht stimmt.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-...** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
