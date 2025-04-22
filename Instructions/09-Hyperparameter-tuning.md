---
lab:
  title: Durchführen der Hyperparameteroptimierung mit einem Sweepauftrag
---

# Durchführen der Hyperparameteroptimierung mit einem Sweepauftrag

Hyperparameter sind Variablen, die sich darauf auswirken, wie ein Modell trainiert wird, aber nicht von den Trainingsdaten abgeleitet werden können. Die Auswahl der optimalen Hyperparameterwerte für das Trainieren von Modellen kann schwierig sein und bedeutet in der Regel vielfaches Ausprobieren.

In dieser Übung verwenden Sie Azure Machine Learning, um Hyperparameter zu optimieren, indem Sie mehrere Trainingsläufe parallel ausführen.

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
1. Überprüfen Sie, ob das richtige Abonnement angegeben ist und ob **kein Speicherkonto** ausgewählt ist. Wählen Sie **Übernehmen**.
1. Geben Sie im Terminal die folgenden Befehle ein, um dieses Repository zu klonen:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Kopieren Sie mit `SHIFT + INSERT` Ihren Code in die Cloud Shell.

1. Nachdem das Repository geklont wurde, geben Sie die folgenden Befehle ein, um in den Ordner für dieses Lab zu wechseln. Führen Sie das darin enthaltene Skript **setup.sh** aus:

    ```azurecli
    cd azure-ml-labs/Labs/09
    ./setup.sh
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Erweiterungen nicht installiert wurden.

1. Warten Sie, bis das Skript abgeschlossen ist. Dies dauert in der Regel etwa 5–10 Minuten.

    <details>
    <summary><b>Tipp zur Fehlerbehebung</b>: Fehler bei der Erstellung des Arbeitsbereichs</summary><br>
    <p>Wenn Sie beim Ausführen des Setup-Skripts über die Befehlszeilenschnittstelle einen Fehler empfangen, müssen Sie die Ressourcen manuell bereitstellen:</p>
    <ol>
        <li>Wählen Sie auf der Startseite des Azure-Portals die Option <b>+ Erstellen einer Ressource</b>.</li>
        <li>Suchen Sie nach <i>Maschinelles Lernen</i> und wählen Sie dann <b>Azure Machine Learning</b>. Klicken Sie auf <b>Erstellen</b>.</li>
        <li>Erstellen Sie eine neue Azure Machine Learning-Ressource mit den folgenden Einstellungen: <ul>
                <li><b>Abonnement:</b> <i>Geben Sie Ihr Azure-Abonnement an.</i></li>
                <li><b>Ressourcengruppe</b>: rg-dp100-labs</li>
                <li><b>Arbeitsbereichsname</b>: mlw-dp100-labs</li>
                <li><b>Region:</b><i>Wählen Sie die nächstgelegene geografische Region aus.</i></li>
                <li><b>Speicherkonto:</b><i>Für Ihren Arbeitsbereich wird standardmäßig ein neues Speicherkonto erstellt.</i></li>
                <li><b>Schlüsseltresor:</b><i>Für Ihren Arbeitsbereich wird standardmäßig ein neuer Schlüsseltresor erstellt.</i></li>
                <li><b>Application Insights:</b><i>Für Ihren Arbeitsbereich wird standardmäßig eine neue Application Insights-Ressource erstellt.</i></li>
                <li><b>Containerregistrierung:</b> Keine (<i>wird automatisch erstellt, wenn Sie das erste Mal ein Modell in einem Container bereitstellen</i>)</li>
            </ul>
        <li>Wählen Sie <b>Review + create</b> und warten Sie, bis der Arbeitsbereich und die ihm zugeordneten Ressourcen erstellt sind - dies dauert in der Regel etwa 5 Minuten.</li>
        <li>Wählen Sie <b>Zur Ressource gehen</b> und auf der Seite <b>Übersicht</b>, wählen Sie <b>Studio starten</b>. In Ihrem Browser wird eine weitere Registerkarte geöffnet, auf der Azure Machine Learning Studio geöffnet wird.</li>
        <li>Schließen Sie alle Popupelemente, die in Studio angezeigt werden.</li>
        <li>Navigieren Sie in Azure Machine Learning Studio zur Seite <b>Compute</b> und wählen Sie <b>+Neu</b> unter der Registerkarte <b>Compute-Instanzen</b>.</li>
        <li>Geben Sie der Instanz einen eindeutigen Namen und wählen Sie <b>Standard_DS11_v2</b> als Größe des virtuellen Computers.</li>
        <li>Klicken Sie auf <b>Überprüfen und erstellen</b> und dann auf <b>Erstellen</b>.</li>
        <li>Wählen Sie dann die Registerkarte <b>Computecluster</b> und wählen Sie <b>+ New</b>.</li>
        <li>Wählen Sie dieselbe Region wie die, in der Sie Ihren Arbeitsbereich erstellt haben, und wählen Sie dann <b>Standard-DS11-v2</b> als Größe des virtuellen Computers. Wählen Sie <b>Weiter</b> aus.</li>
        <li>Geben Sie dem Cluster einen eindeutigen Namen und wählen Sie dann <b>Erstellen</b>.</li>
    </ol>
    </details>

## Klonen der Labmaterialien

Wenn Sie den Arbeitsbereich und die erforderlichen Computeressourcen erstellt haben, können Sie Azure Machine Learning Studio öffnen und die Labmaterialien in den Arbeitsbereich klonen.

1. Navigieren Sie im Azure-Portal zum Azure Machine Learning-Arbeitsbereich mit dem Namen **mlw-dp100-...** .
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

## Optimieren von Hyperparametern mit einem Sweepauftrag

Sie verfügen nun über alle erforderlichen Ressourcen und können das Notebook ausführen, um einen Sweepauftrag zu übermitteln.

1. Öffnen Sie das Notebook **Labs/09/Hyperparameter tuning.ipynb**.

    > Wählen Sie **Authentifizieren** aus, und führen Sie die erforderlichen Schritte aus, wenn eine Benachrichtigung angezeigt wird, in der Sie zur Authentifizierung aufgefordert werden.

1. Stellen Sie sicher, dass das Notebook den Kernel **Python 3.8 – AzureML** verwendet.
1. Führen Sie alle Zellen im Notebook aus.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-...** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
