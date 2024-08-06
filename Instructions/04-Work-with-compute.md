---
lab:
  title: Arbeiten mit Computeressourcen in Azure Machine Learning
---

# Arbeiten mit Computeressourcen in Azure Machine Learning

Einer der Hauptvorteile der Cloud ist die Möglichkeit, skalierbare Computeressourcen bedarfsgesteuert für die kostengünstige Verarbeitung großer Datenmengen zu verwenden.

In dieser Übung erfahren Sie, wie Sie mithilfe von Cloudcomputing in Azure Machine Learning Experimente und Produktionscode im großen Stil ausführen.

## Vorbereitung

Sie benötigen ein [Azure-Abonnement](https://azure.microsoft.com/free?azure-portal=true), in dem Sie Administratorzugriff besitzen.

## Bereitstellung eines Azure Machine Learning-Arbeitsbereichs

Ein Azure Machine Learning-*Arbeitsbereich* ist eine Zentrale zum Verwalten aller Daten- und anderen Ressourcen, die Sie zum Trainieren und Verwalten Ihrer Modelle benötigen. Sie können mit dem Azure Machine Learning-Arbeitsbereich über Studio, das Python SDK und die Azure CLI interagieren.

Zum Erstellen des Azure Machine Learning-Arbeitsbereichs verwenden Sie die Azure CLI. Alle erforderlichen Befehle sind in einem Shellskript gruppiert, das Sie ausführen können.

1. Öffnen Sie in einem Browser unter `https://portal.azure.com/` das Azure-Portal, und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Wählen Sie oben auf der Seite rechts neben dem Suchfeld die Schaltfläche \[>_] (*Cloud Shell*) aus. Dadurch wird am unteren Rand des Portals ein Cloud Shell-Bereich geöffnet.
1. Wählen Sie bei Aufforderung **Bash** aus. Wenn Sie die Cloud Shell erstmals öffnen, werden Sie zur Wahl der gewünschten Shell (*Bash* oder *PowerShell*) aufgefordert.
1. Überprüfen Sie, ob das richtige Abonnement angegeben ist und ob **kein Speicherkonto** ausgewählt ist. Wählen Sie **Übernehmen**.
1. Um Konflikte mit früheren Versionen zu vermeiden, entfernen Sie alle ML CLI-Erweiterungen (sowohl Version 1 als auch 2), indem Sie diesen Befehl im Terminal ausführen:

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

1. Warten Sie, bis der Befehl abgeschlossen ist. Dies dauert in der Regel etwa 5–10 Minuten.

## Erstellen des Computesetupskripts

Zum Ausführen von Notebooks im Azure Machine Learning-Arbeitsbereich benötigen Sie eine Compute-Instanz. Sie können ein Setupskript verwenden, um die Compute-Instanz bei der Erstellung zu konfigurieren.

1. Navigieren Sie im Azure-Portal zum Azure Machine Learning-Arbeitsbereich mit dem Namen **mlw-dp100-labs**.
1. Wählen Sie den Azure Machine Learning-Arbeitsbereich und dann auf der Seite **Übersicht** die Option **Studio starten** aus. In Ihrem Browser wird eine weitere Registerkarte geöffnet, auf der Azure Machine Learning Studio geöffnet wird.
1. Schließen Sie alle Popupelemente, die in Studio angezeigt werden.
1. Navigieren Sie in Azure Machine Learning Studio zur Seite **Notebooks**.
1. Wählen Sie im Bereich **Dateien** das &#10753;-Symbol zum **Hinzufügen von Dateien** aus.
1. Klicken Sie auf **Create new file** (Neue Datei erstellen).
1. Vergewissern Sie sich, dass der Dateispeicherort **Users/* Ihr-Benutzername*** lautet.
1. Ändern Sie den Dateityp in **Bash (*.sh)** .
1. Ändern Sie den Dateinamen in `compute-setup.sh`.
1. Öffnen Sie die neu erstellte Datei **compute-setup.sh**, und fügen Sie Folgendes in den Inhalt ein:

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Speichern Sie die Datei **compute-setup.sh**.

## Erstellen der Compute-Instanz

Zum Erstellen der Compute-Instanz können Sie Studio, Python SDK oder die Azure CLI verwenden. Sie verwenden Studio, um die Compute-Instanz mit dem soeben erstellten Setupskript zu erstellen.

1. Navigieren Sie über das Menü links zur Seite **Compute**.
1. Wählen Sie auf der Registerkarte **Compute-Instanzen**die Option **Neu** aus.
1. Konfigurieren die Compute-Instanz mit den folgenden Einstellungen (erstellen Sie sie noch nicht): 
    - **Computename:***Geben Sie einen eindeutigen Namen ein.*
    - **VM-Typ**: *CPU*
    - **VM-Größe**: *Standard_DS11_v2*
1. Wählen Sie **Weiter** aus.
1. Wählen Sie **Zeitplan hinzufügen** aus, und konfigurieren Sie den Zeitplan so, dass die Compute-Instanz jeden Tag um **18:00** Uhr bzw. **6:00 PM** **beendet** wird.
1. Wählen Sie **Weiter** aus.
1. Überprüfen Sie die Sicherheitseinstellungen, aber aktivieren Sie sie **nicht**:
    - **SSH-Zugriff aktivieren**: *Sie können über diese Option den Direktzugriff auf die VM mithilfe eines SSH-Clients aktivieren.*
    - **Virtuelles Netzwerk aktivieren**: *Diese Option wird in der Regel in einer Unternehmensumgebung verwendet, um die Netzwerksicherheit zu verbessern.*
    - **Einem anderen Benutzer zuweisen**: *Sie können diese Option verwenden, um eine Compute-Instanz einem anderen Data Scientist zuzuweisen.*
1. Wählen Sie **Weiter** aus.
1. Wählen Sie die Umschaltfläche für **Mit Erstellungsskript bereitstellen** aus.
1. Wählen Sie das zuvor erstellte Skript **compute-setup.sh** aus.
1. Wählen Sie **Überprüfen und erstellen** aus, um die Computeinstanz zu erstellen. Warten Sie, bis sie gestartet wurde und sich der Status in **Wird ausgeführt** geändert hat.
1. Wenn die Compute-Instanz ausgeführt wird, navigieren Sie zur Seite **Notebooks**. Klicken Sie im Bereich **Dateien** auf **&#8635;** , um die Ansicht zu aktualisieren und sicherzustellen, dass der neue Ordner **Users/*Ihr-Benutzername*/dp100-azure-ml-labs** erstellt wurde.

## Konfigurieren der Compute-Instanz

Wenn Sie die Compute-Instanz erstellt haben, können Sie Notebooks darauf ausführen. Möglicherweise müssen Sie bestimmte Pakete installieren, um den gewünschten Code auszuführen. Sie können Pakete in das Setupskript aufnehmen oder über das Terminal installieren.

1. Navigieren Sie auf der Registerkarte **Compute-Instanzen** zu Ihrer Compute-Instanz, und wählen Sie die Anwendung **Terminal** aus.
1. Installieren Sie im Terminal das Python SDK in der Compute-Instanz, indem Sie in Terminal die folgenden Befehle ausführen:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Pakete nicht installiert wurden.

1. Wenn die Pakete installiert sind, können Sie die Registerkarte schließen, um das Terminal zu beenden.

## Erstellen eines Computeclusters

Notebooks eignen sich ideal für die Entwicklung oder iterative Arbeit während des Experimentierens. Beim Experimentieren sollten Sie Notebooks auf einer Compute-Instanz ausführen, um Code schnell zu testen und zu überprüfen. Wenn Sie zur Produktion wechseln, sollten Sie Skripts auf einem Computecluster ausführen. Sie erstellen einen Computecluster mit dem Python SDK und verwenden ihn dann, um ein Skript als Auftrag auszuführen.

1. Öffnen Sie das Notebook **Labs/04/Work with compute.ipynb**.

    > Wählen Sie **Authentifizieren** aus, und führen Sie die erforderlichen Schritte aus, wenn eine Benachrichtigung angezeigt wird, in der Sie zur Authentifizierung aufgefordert werden.

1. Stellen Sie sicher, dass das Notebook den Kernel **Python 3.8 – AzureML** verwendet.
1. Führen Sie alle Zellen im Notebook aus.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-labs** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
