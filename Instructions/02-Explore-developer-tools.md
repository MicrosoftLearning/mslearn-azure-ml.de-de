---
lab:
  title: Erkunden von Entwicklertools für die Interaktion mit dem Arbeitsbereich
---

# Erkunden von Entwicklertools für die Interaktion mit dem Arbeitsbereich

Sie können verschiedene Tools verwenden, um mit dem Azure Machine Learning-Arbeitsbereich zu interagieren. Je nachdem, welche Aufgabe Sie ausführen müssen und welches Entwicklertool Sie bevorzugen, können Sie auswählen, welches Tool wann verwendet werden soll. Dieses Lab ist als Einführung in die Entwicklertools konzipiert, die häufig für Arbeitsbereichsinteraktionen verwendet werden. Wenn Sie die Verwendung eines bestimmten Tools vertiefen möchten, stehen Ihnen weitere Labs zur Verfügung.

## Vorbereitung

Sie benötigen ein [Azure-Abonnement](https://azure.microsoft.com/free?azure-portal=true), in dem Sie Administratorzugriff besitzen.

Die folgenden Entwicklertools werden häufig für die Interaktion mit dem Azure Machine Learning-Arbeitsbereich verwendet:

- **Azure CLI** mit der Azure Machine Learning-Erweiterung: Dieser Befehlszeilenansatz eignet sich ideal für die Automatisierung der Infrastruktur.
- **Azure Machine Learning Studio**: Verwenden Sie die benutzerfreundliche Benutzeroberfläche, um den Arbeitsbereich und alle seine Funktionen zu erkunden.
- **Python SDK** für Azure Machine Learning: Verwenden Sie das Python SDK zum Übermitteln von Aufträgen und zum Verwalten von Modellen aus einem Jupyter-Notebook. Es eignet sich ideal für Data Scientists.

Sie werden jedes dieser Tools für Aufgaben untersuchen, die üblicherweise mit dem jeweiligen Tool erledigt werden.

## Bereitstellen der Infrastruktur mit der Azure CLI

Damit ein Data Scientist ein Machine Learning-Modell mit Azure Machine Learning trainieren kann, müssen Sie die erforderliche Infrastruktur einrichten. Sie können die Azure CLI mit der Azure Machine Learning-Erweiterung verwenden, um einen Azure Machine Learning-Arbeitsbereich und Ressourcen wie eine Compute-Instanz zu erstellen.

Öffnen Sie zunächst die Azure Cloud Shell, installieren Sie die Azure Machine Learning-Erweiterung, und klonen Sie das Git-Repository.

1. Öffnen Sie in einem Browser das Azure-Portal unter `https://portal.azure.com/`, und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Wählen Sie oben auf der Seite rechts neben dem Suchfeld die Schaltfläche \[>_] (*Cloud Shell*) aus. Dadurch wird am unteren Rand des Portals ein Cloud Shell-Bereich geöffnet.
1. Wählen Sie bei Aufforderung **Bash** aus. Wenn Sie die Cloud Shell erstmals öffnen, werden Sie zur Wahl der gewünschten Shell (*Bash* oder *PowerShell*) aufgefordert.
1. Stellen Sie sicher, dass das gewünschte Abonnement angegeben ist, und wählen Sie **Speicher erstellen** aus, wenn Sie aufgefordert werden, Speicher für Ihre Cloud Shell zu erstellen. Warten Sie, bis der Speicher erstellt wurde.
1. Entfernen Sie alle ML CLI-Erweiterungen (sowohl Version 1 als auch 2), um Konflikte früherer Versionen mit diesem Befehl zu vermeiden:
    
    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Kopieren Sie mit `SHIFT + INSERT` Ihren Code in die Cloud Shell.

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Erweiterungen nicht installiert wurden.

1. Installieren Sie die Azure Machine Learning-Erweiterung (v2) mit dem folgenden Befehl:
    
    ```azurecli
    az extension add -n ml -y
    ```

1. Erstellen Sie eine Ressourcengruppe. Wählen Sie einen Standort in Ihrer Nähe aus.
    
    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. Erstellen Sie einen Arbeitsbereich:
    
    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. Warten Sie, bis der Arbeitsbereich und die zugehörigen Ressourcen erstellt wurden, was in der Regel etwa 5 Minuten dauert.

## Erstellen einer Compute-Instanz mit der Azure CLI

Ein weiterer wichtiger Teil der Infrastruktur, die zum Trainieren eines Machine Learning-Modells benötigt wird, sind **Computeressourcen**. Obwohl Sie Modelle lokal trainieren können, bietet die Verwendung von Cloudcomputing mehr Skalierbarkeit und Kosteneffizienz.

Wenn Data Scientists ein Machine Learning-Modell im Azure Machine Learning-Arbeitsbereich entwickeln, möchten sie einen virtuellen Computer für die Ausführung von Jupyter-Notebooks verwenden. Für die Entwicklung eignet sich eine **Compute-Instanz** ideal.

Nach dem Erstellen eines Azure Machine Learning-Arbeitsbereichs können Sie auch eine Compute-Instanz mit der Azure CLI erstellen.

In dieser Übung erstellen Sie eine Compute-Instanz mit den folgenden Einstellungen:

- **Computename**: *Name der Compute-Instanz. Muss eindeutig sein und weniger als 24 Zeichen enthalten.*
- **VM-Größe**: STANDARD_DS11_V2
- **Computetyp** (Instanz oder Cluster): ComputeInstance
- **Name des Azure Machine Learning-Arbeitsbereichs**: mlw-dp100-labs
- **Ressourcengruppe**: rg-dp100-labs

1. Verwenden Sie den folgenden Befehl, um eine Compute-Instanz in Ihrem Arbeitsbereich zu erstellen. Wenn der Name der Compute-Instanz „XXXX“ enthält, ersetzen Sie die Zeichen durch Zufallszahlen, um einen eindeutigen Namen zu erstellen.

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    Wenn Sie eine Fehlermeldung erhalten, die besagt, dass eine Compute-Instanz mit dem Namen bereits vorhanden ist, ändern Sie den Namen, und wiederholen Sie den Befehl.

## Erstellen eines Computeclusters mit der Azure CLI

Eine Compute-Instanz ist zwar für die Entwicklung ideal, ein Computecluster eignet sich jedoch besser, wenn Machine Learning-Modelle trainiert werden sollen. Erst bei Übermittlung eines Auftrags zur Verwendung des Computeclusters wird die Größe auf mehr als 0 Knoten geändert und der Auftrag ausgeführt. Sobald der Computecluster nicht mehr benötigt wird, wird die Größe automatisch wieder auf 0 Knoten geändert, um die Kosten zu minimieren. 

Zum Erstellen eines Computeclusters können Sie die Azure CLI verwenden, ähnlich wie beim Erstellen einer Compute-Instanz.

Sie erstellen einen Computecluster mit den folgenden Einstellungen:

- **Computename**: aml-cluster
- **VM-Größe**: STANDARD_DS11_V2
- **Computetyp**: AmlCompute *(Erstellt einen Computecluster)*
- **Maximale Instanzen**: *Maximale Anzahl von Knoten*
- **Name des Azure Machine Learning-Arbeitsbereichs**: mlw-dp100-labs
- **Ressourcengruppe**: rg-dp100-labs

1. Verwenden Sie den folgenden Befehl, um einen Computecluster in Ihrem Arbeitsbereich zu erstellen.
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## Konfigurieren Ihrer Workstation mit Azure Machine Learning Studio

Obwohl die Azure CLI ideal für die Automatisierung geeignet ist, sollten Sie die Ausgabe der von Ihnen ausgeführten Befehle überprüfen. Sie können Azure Machine Learning Studio verwenden, um zu überprüfen, ob Ressourcen und Objekte erstellt und Aufträge erfolgreich ausgeführt wurden, oder um festzustellen, warum ein Auftrag fehlgeschlagen ist. 

1. Navigieren Sie im Azure-Portal zum Azure Machine Learning-Arbeitsbereich mit dem Namen **mlw-dp100-labs**.
1. Wählen Sie den Azure Machine Learning-Arbeitsbereich und dann auf der Seite **Übersicht** die Option **Studio starten** aus. In Ihrem Browser wird eine weitere Registerkarte geöffnet, auf der Azure Machine Learning Studio geöffnet wird.
1. Schließen Sie alle Popupelemente, die in Studio angezeigt werden.
1. Navigieren Sie innerhalb von Azure Machine Learning Studio zur Seite **Compute**, und überprüfen Sie, ob die Compute-Instanz und der Cluster vorhanden sind, die Sie im vorherigen Abschnitt erstellt haben. Die Computeressource sollte ausgeführt werden, der Cluster sollte sich mit 0 ausgeführten Knoten im Leerlauf befinden.

## Verwenden des Python SDK, um ein Modell zu trainieren

Nachdem Sie sich vergewissert haben, dass die erforderliche Compute-Instanz erstellt wurde, können Sie nun mit dem Python SDK ein Trainingsskript ausführen. Sie installieren und verwenden das Python SDK auf der Compute-Instanz und trainieren das Machine Learning-Modell im Computecluster.

1. Wählen Sie die **Terminalanwendung** für Ihre **Compute-Instanz** aus, um das Terminal zu starten.
1. Installieren Sie im Terminal das Python SDK in der Compute-Instanz, indem Sie in Terminal die folgenden Befehle ausführen:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Pakete nicht installiert wurden.

1. Führen Sie den folgenden Befehl aus, um ein Git-Repository mit Notebooks, Daten und anderen Dateien in Ihrem Arbeitsbereich zu klonen:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Wenn der Befehl abgeschlossen ist, wählen Sie im Bereich **Dateien** die Option **&#8635;** aus, um die Ansicht zu aktualisieren und sicherzustellen, dass der neue Ordner **Users/*Ihr-Benutzername*/azure-ml-labs** erstellt wurde.
1. Öffnen Sie das Notebook **Labs/02/Run training script.ipynb**.

    > Wählen Sie **Authentifizieren** aus, und führen Sie die erforderlichen Schritte aus, wenn eine Benachrichtigung angezeigt wird, in der Sie zur Authentifizierung aufgefordert werden.

1. Stellen Sie sicher, dass das Notebook den Kernel **Python 3.8 – AzureML** verwendet. Jeder Kernel verfügt über ein eigenes Image mit einem eigenen vorinstallierten Paketsatz.
1. Führen Sie alle Zellen im Notebook aus.

Im Azure Machine Learning-Arbeitsbereich wird ein neuer Auftrag erstellt. Der Auftrag verfolgt die in der Auftragskonfiguration definierten Eingaben, den verwendeten Code und die Ausgaben wie Metriken zum Auswerten des Modells nach.

## Überprüfen Ihres Auftragsverlaufs in Azure Machine Learning Studio

Wenn Sie einen Auftrag an den Azure Machine Learning-Arbeitsbereich übermitteln, können Sie dessen Status in Azure Machine Learning Studio überprüfen.

1. Wählen Sie entweder die im Notebook als Ausgabe angegeben Auftrags-URL aus, oder navigieren Sie zur Seite **Aufträge** in Azure Machine Learning Studio.
1. Ein neues Experiment mit dem Namen **diabetes-training** ist aufgeführt. Wählen Sie den neuesten Auftrag **diabetes-pythonv2-train** aus.
1. Überprüfen Sie die **Eigenschaften** des Auftrags. Beachten Sie den **Status** des Auftrags:
    - **In der Warteschlange**: Der Auftrag wartet darauf, dass die Compute-Instanz verfügbar wird.
    - **Wird vorbereitet**: Die Größe des Computeclusters wird geändert, oder die Umgebung wird auf dem Computeziel installiert.
    - **Wird ausgeführt**: Das Trainingsskript wird ausgeführt.
    - **Wird abgeschlossen**: Das Trainingsskript wurde ausgeführt, und der Auftrag wird mit allen endgültigen Informationen aktualisiert.
    - **Abgeschlossen**: Der Auftrag wurde erfolgreich abgeschlossen und beendet.
    - **Fehler**: Der Auftrag ist fehlgeschlagen und wird beendet.
1. Unter **Ausgaben + Protokolle** finden Sie die Ausgabe des Skripts in **user_logs/std_log.txt**. Die Ausgabe von **print**-Anweisungen im Skript wird hier angezeigt. Wenn aufgrund eines Skriptproblems ein Fehler auftritt, finden Sie die Fehlermeldung auch hier.
1. Unter **Code** finden Sie den Ordner, den Sie in der Auftragskonfiguration angegeben haben. Dieser Ordner enthält das Trainingsskript und das Dataset.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-labs** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus. 
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
