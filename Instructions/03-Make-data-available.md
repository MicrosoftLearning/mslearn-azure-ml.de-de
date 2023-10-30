---
lab:
  title: Verfügbarmachen von Daten in Azure Machine Learning
---

# Verfügbarmachen von Daten in Azure Machine Learning

Obwohl es recht üblich ist, mit Daten in ihrem lokalen Dateisystem zu arbeiten, kann es in einer Unternehmensumgebung effektiver sein, die Daten an einem zentralen Ort zu speichern, an dem mehrere Data Scientists und Machine Learning-Techniker darauf zugreifen können.

In dieser Übung befassen Sie sich mit *Datenspeichern* und *Datenressourcen*. Hierbei handelt es sich um die primären Objekte, die zum Abstrahieren des Datenzugriffs in Azure Machine Learning verwendet werden.

## Vorbereitung

Sie benötigen ein [Azure-Abonnement](https://azure.microsoft.com/free?azure-portal=true), in dem Sie Administratorzugriff besitzen.

## Bereitstellung eines Azure Machine Learning-Arbeitsbereichs

Ein Azure Machine Learning-*Arbeitsbereich* ist eine Zentrale zum Verwalten aller Daten- und anderen Ressourcen, die Sie zum Trainieren und Verwalten Ihrer Modelle benötigen. Sie können mit dem Azure Machine Learning-Arbeitsbereich über Studio, das Python SDK und die Azure CLI interagieren.

Sie verwenden ein Shellskript, das den Arbeitsbereich und die erforderlichen Ressourcen mithilfe der Azure CLI bereitstellt. Als Nächstes trainieren und vergleichen Sie Modelle mit dem Designer in Azure Machine Learning Studio.

### Erstellen des Arbeitsbereichs und der Computeressourcen

Zum Erstellen des Azure Machine Learning-Arbeitsbereichs und der Computeressourcen verwenden Sie die Azure CLI. Alle erforderlichen Befehle sind in einem Shellskript gruppiert, das Sie ausführen können.

1. Öffnen Sie in einem Browser unter `https://portal.azure.com/` das Azure-Portal, und melden Sie sich mit Ihrem Microsoft-Konto an.
1. Wählen Sie oben auf der Seite rechts neben dem Suchfeld die Schaltfläche \[>_] (*Cloud Shell*) aus. Dadurch wird am unteren Rand des Portals ein Cloud Shell-Bereich geöffnet.
1. Wählen Sie bei Aufforderung **Bash** aus. Wenn Sie die Cloud Shell erstmals öffnen, werden Sie zur Wahl der gewünschten Shell (*Bash* oder *PowerShell*) aufgefordert.
1. Stellen Sie sicher, dass das gewünschte Abonnement angegeben ist, und wählen Sie **Speicher erstellen** aus, wenn Sie aufgefordert werden, Speicher für Ihre Cloud Shell zu erstellen. Warten Sie, bis der Speicher erstellt wurde.
1. Geben Sie die folgenden Befehle im Terminal ein, um dieses Repository zu klonen:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Kopieren Sie mit `SHIFT + INSERT` Ihren Code in die Cloud Shell.

1. Geben Sie die folgenden Befehle ein, nachdem das Repository geklont wurde, um in den Ordner für dieses Lab zu wechseln. Führen Sie das darin enthaltene Skript **setup.sh** aus:

    ```azurecli
    cd azure-ml-labs/Labs/03
    ./setup.sh
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Erweiterungen nicht installiert wurden.

1. Warten Sie, bis das Skript abgeschlossen ist. Dies dauert in der Regel etwa 5–10 Minuten.

## Erkunden der Standarddatenspeicher

Wenn Sie einen Azure Machine Learning-Arbeitsbereich erstellen, wird automatisch ein Speicherkonto erstellt und mit Ihrem Arbeitsbereich verbunden. Sie erfahren, wie das Speicherkonto verbunden wird.

1. Navigieren Sie im Azure-Portal zur neuen Ressourcengruppe mit dem Namen **rg-dp100-...** .
1. Wählen Sie das Speicherkonto in der Ressourcengruppe aus. Der Name beginnt häufig mit dem Namen, den Sie für den Arbeitsbereich angegeben haben (ohne Bindestriche).
1. Schauen Sie sich die Seite **Übersicht** des Speicherkontos an. Beachten Sie, dass das Speicherkonto über mehrere Optionen für die **Datenspeicherung** verfügt, wie im Bereich „Übersicht“ und im linken Menü gezeigt.
1. Wählen Sie **Container** aus, um den Teil „Blobspeicher“ des Speicherkontos zu erkunden.
1. Beachten Sie den Container **azureml-blobstore-...** . Der Standarddatenspeicher für Datenressourcen verwendet diesen Container zum Speichern von Daten.
1. Erstellen Sie über die Schaltfläche &#43; **Container** am oberen Bildschirmrand einen neuen Container, und nennen Sie ihn `training-data`.
1. Wählen Sie im linken Menü **Dateifreigaben** aus, um den Teil „Dateifreigabe“ des Speicherkontos zu erkunden.
1. Beachten Sie die Dateifreigabe **code-...** . Alle Notebooks im Arbeitsbereich werden hier gespeichert. Nach dem Klonen der Labmaterialien finden Sie die Dateien in dieser Dateifreigabe im Ordner **code-.../Users/*Ihr-Benutzername*/azure-ml-labs**.

## Kopieren des Zugriffsschlüssels

Um einen Datenspeicher im Azure Machine Learning-Arbeitsbereich zu erstellen, müssen Sie bestimmte Anmeldeinformationen angeben. Eine einfache Möglichkeit, dem Arbeitsbereich Zugriff auf einen Blobspeicher zu gewähren, ist die Verwendung des Kontoschlüssels.

1. Wählen Sie im Speicherkonto im linken Menü die Registerkarte **Zugriffsschlüssel** aus.
1. Beachten Sie, dass zwei Schlüssel bereitgestellt werden: „key1“ und „key2“. Jeder Schlüssel verfügt über die gleiche Funktionalität. 
1. Wählen Sie unter **key1** die Option **Anzeigen** für das Feld **Key**.
1. Kopieren Sie den Wert des Felds **Schlüssel** in einen Editor. Sie müssen diesen Wert später in das Notebook einfügen.
1. Kopieren Sie den Namen Ihres Speicherkontos oben auf der Seite. Der Name sollte mit **mlwdp100storage...** beginnen. Sie müssen auch diesen Wert später in das Notebook einfügen.

> **Hinweis**: Kopieren Sie den Kontoschlüssel und den Namen in einen Editor, um eine automatische Großschreibung zu vermeiden (was in Word geschieht). Beim Schlüssel wird die Groß-/Kleinschreibung berücksichtigt.

## Klonen der Labmaterialien

Um einen Datenspeicher und Datenressourcen mit dem Python SDK zu erstellen, müssen Sie die Labmaterialien in den Arbeitsbereich klonen.

1. Navigieren Sie im Azure-Portal zum Azure Machine Learning-Arbeitsbereich mit dem Namen **mlw-dp100-labs**.
1. Wählen Sie den Azure Machine Learning-Arbeitsbereich und dann auf der Seite **Übersicht** die Option **Studio starten** aus. In Ihrem Browser wird eine weitere Registerkarte geöffnet, auf der Azure Machine Learning Studio geöffnet wird.
1. Schließen Sie alle Popupelemente, die in Studio angezeigt werden.
1. Navigieren Sie innerhalb von Azure Machine Learning Studio zur Seite **Compute**, und überprüfen Sie, ob die Compute-Instanz und der Cluster vorhanden sind, die Sie im vorherigen Abschnitt erstellt haben. Die Compute-Instanz sollte ausgeführt werden, der Cluster sollte sich mit 0 ausgeführten Knoten im Leerlauf befinden.
1. Navigieren Sie auf der Registerkarte **Compute-Instanzen** zu Ihrer Compute-Instanz, und wählen Sie die Anwendung **Terminal** aus.
1. Installieren Sie im Terminal das Python SDK in der Compute-Instanz, indem Sie in Terminal die folgenden Befehle ausführen:

    ```azurecli
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mltable
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Pakete nicht installiert wurden.

1. Führen Sie den folgenden Befehl aus, um ein Git-Repository mit Notebooks, Daten und anderen Dateien in Ihrem Arbeitsbereich zu klonen:

    ```azurecli
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Wenn der Befehl abgeschlossen ist, klicken Sie im Bereich **Dateien** auf **&#8635;** , um die Ansicht zu aktualisieren und sicherzustellen, dass der neue Ordner **Users/*Ihr-Benutzername*/azure-ml-labs** erstellt wurde.

**Optional** können Sie auf einer anderen Registerkarte des Browsers zurück zum [Azure-Portal](https://portal.azure.com?azure-portal=true) navigieren. Schauen Sie sich die Dateifreigabe **code-...** im Speicherkonto erneut an, und suchen Sie die geklonten Labmaterialien im neu erstellten Ordner **azure-ml-labs**.

## Erstellen von Datenspeicher und Datenressourcen

Der Code zum Erstellen von Datenspeicher und Datenressourcen mit dem Python SDK wird in einem Notebook bereitgestellt.

1. Öffnen Sie das Notebook **Labs/03/Work with data.ipynb**.

    > Wählen Sie **Authentifizieren** aus, und führen Sie die erforderlichen Schritte aus, wenn eine Benachrichtigung angezeigt wird, in der Sie zur Authentifizierung aufgefordert werden.

1. Stellen Sie sicher, dass das Notebook den Kernel **Python 3.8 – AzureML** verwendet.
1. Führen Sie alle Zellen im Notebook aus.

## Optional: Erkunden der Datenressourcen

**Optional** können Sie untersuchen, wie die Datenressourcen im zugeordneten Speicherkonto gespeichert werden.

1. Navigieren Sie in Azure Machine Learning Studio zur Registerkarte **Daten**, um die Datenressourcen zu erkunden.
1. Wählen Sie den Namen des Datenobjekts **diabetes-local** aus, um die entsprechenden Details zu untersuchen. 

    Unter **Datenquellen** für das Datenobjekt **diabetes-lokale** wird angezeigt, wohin die Datei hochgeladen wurde. Der Pfad, der mit **LocalUpload/...** beginnt, zeigt den Pfad innerhalb des Speicherkontocontainers **azureml-blobstore-...** . Um zu überprüfen, ob die Datei vorhanden ist, navigieren Sie im Azure-Portal zu diesem Pfad.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-...** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
