---
lab:
  title: Trainieren eines Modells mit Azure Machine Learning Designer
---

# Trainieren eines Modells mit Azure Machine Learning Designer

Azure Machine Learning Designer bietet eine Benutzeroberfläche mit Drag & Drop-Funktionalität, auf der Sie einen Workflow definieren können. Sie können ganz einfach einen Workflow erstellen, um ein Modell zu trainieren, mehrere Algorithmen zu testen und diese zu vergleichen.

In dieser Übung verwenden Sie den Designer, um zwei Klassifizierungsalgorithmen schnell zu trainieren und zu vergleichen.

## Vorbereitung

Sie benötigen ein [Azure-Abonnement](https://azure.microsoft.com/free?azure-portal=true), in dem Sie Administratorzugriff besitzen.

## Bereitstellung eines Azure Machine Learning-Arbeitsbereichs

Ein Azure Machine Learning-*Arbeitsbereich* ist eine Zentrale zum Verwalten aller Daten- und anderen Ressourcen, die Sie zum Trainieren und Verwalten Ihrer Modelle benötigen. Sie können mit dem Azure Machine Learning-Arbeitsbereich über Studio, das Python SDK und die Azure CLI interagieren.

Sie verwenden ein Shellskript, das den Arbeitsbereich und die erforderlichen Ressourcen mithilfe der Azure CLI bereitstellt. Als Nächstes trainieren und vergleichen Sie Modelle mit dem Designer in Azure Machine Learning Studio.

### Erstellen des Arbeitsbereichs und des Computeclusters

Zum Erstellen des Azure Machine Learning-Arbeitsbereichs und eines Computeclusters verwenden Sie die Azure CLI. Alle erforderlichen Befehle sind in einem Shellskript gruppiert, das Sie ausführen können.

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

1. Nachdem das Repository geklont wurde, geben Sie die folgenden Befehle ein, um in den Ordner für dieses Lab zu wechseln. Führen Sie das darin enthaltene Skript „setup.sh“ aus:

    ```azurecli
    cd azure-ml-labs/Labs/05
    ./setup.sh
    ```

    > Ignorieren Sie alle (Fehler-) Meldungen, die besagen, dass die Erweiterungen nicht installiert wurden.

1. Warten Sie, bis das Skript abgeschlossen ist. Dies dauert in der Regel etwa 5–10 Minuten.

## Konfigurieren einer neuen Pipeline

Wenn Sie den Arbeitsbereich und erforderlichen Computecluster erstellt haben, können Sie Azure Machine Learning Studio öffnen und mit dem Designer eine Trainingspipeline erstellen.

1. Navigieren Sie im Azure-Portal zum Azure Machine Learning-Arbeitsbereich mit dem Namen **mlw-dp100-...** .
1. Wählen Sie den Azure Machine Learning-Arbeitsbereich und dann auf der Seite **Übersicht** die Option **Studio starten** aus. In Ihrem Browser wird eine weitere Registerkarte geöffnet, auf der Azure Machine Learning Studio geöffnet wird.
1. Schließen Sie alle Popupelemente, die in Studio angezeigt werden.
1. Navigieren Sie in Azure Machine Learning Studio zur Seite **Compute**, und überprüfen Sie, ob der Computecluster vorhanden ist, den Sie im vorherigen Abschnitt erstellt haben. Der Cluster sollte sich mit 0 ausgeführten Knoten im Leerlauf befinden.
1. Wechseln Sie zur Seite **Designer**.
1. Wählen Sie oben auf der Seite die Registerkarte **Benutzerdefiniert** aus.
1. Erstellen Sie mithilfe benutzerdefinierter Komponenten eine neue leere Pipeline.
1. Ändern Sie den Standardpipelinenamen (**Pipeline-Created-on-* date***) in `Train-Diabetes-Classifier` indem Sie das Stiftsymbol auf der rechten Seite auswählen.


## Erstellen einer neuen Pipeline

Zum Trainieren eines Modells benötigen Sie Daten. Sie können alle in einem Datenspeicher gespeicherten Daten oder eine öffentlich zugängliche URL verwenden.

1. Wählen Sie im Menü auf der linken Seite die Registerkarte **Daten** aus.
1. Ziehen Sie die Komponente **diabetes-folder** auf den Canvas, und legen Sie sie ab.

    Sie verfügen nun über die gewünschten Daten und können mit dem Erstellen einer Pipeline unter Verwendung benutzerdefinierter Komponenten fortfahren, die bereits im Arbeitsbereich vorhanden sind (während der Einrichtung für Sie erstellt wurden).

1. Wählen Sie im Menü auf der linken Seite die Registerkarte **Komponenten** aus.
1. Ziehen Sie die Komponente **Leere Zeilen entfernen** auf den Canvas, und legen Sie sie unterhalb des Ordners **diabetes-ordner** ab.
1. Verbinden Sie die Ausgabe der Daten mit der Eingabe der neuen Komponente.
1. Ziehen Sie die Komponente **Numerische Spalten normalisieren** auf den Canvas und legen Sie sie unter **Leere Zeilen entfernen** ab.
1. Verbinden Sie die Ausgabe der vorherigen Komponente mit der Eingabe der neuen Komponente.
1. Ziehen Sie die Komponente **Modell für Entscheidungsstrukturklassifizierer trainieren** auf den Canvas und legen Sie sie unter **Numerische Spalten normalisieren** ab.
1. Verbinden Sie die Ausgabe der vorherigen Komponente mit der Eingabe der neuen Komponente.
1. Wählen Sie **Konfigurieren & Übermitteln** aus und erstellen Sie auf der Seite **Pipelineauftrag einrichten** ein neues Experiment. Nennen Sie es `diabetes-designer-pipeline` und wählen Sie dann **Weiter** aus.
1. Nehmen Sie unter **Eingaben & Ausgaben** keine Änderungen vor und wählen Sie **Weiter** aus.
1. Wählen Sie in den **Laufzeiteinstellungen** **Computecluster** aus und wählen Sie unter **Azure ML-Computecluster auswählen** Ihren *aml-Cluster* aus.
1. Wählen Sie **Überprüfen + Übermitteln** und dann **Übermitteln** aus, um die Pipelineausführung zu starten.
1. Sie können den Status der Ausführung überprüfen, indem Sie zur Seite **Pipelines** wechseln und die Pipeline **Train-Diabetes-Classifier** auswählen.
1. Warten Sie, bis alle Komponenten erfolgreich abgeschlossen wurden.

    Beim Übermitteln des Auftrags wird der Computecluster initialisiert. Da sich der Computecluster bisher im Leerlauf befand, kann es einige Zeit dauern, bis die Größe des Clusters auf mehr als 0 Knoten geändert wird. Sobald die Größe des Clusters geändert wurde, wird die Ausführung der Pipeline automatisch gestartet.

Sie können die Ausführung jeder Komponente nachverfolgen. Wenn die Pipeline fehlschlägt, können Sie untersuchen, welche Komponente fehlgeschlagen ist und warum. Fehlermeldungen werden in der Auftragsübersicht auf der Registerkarte **Ausgaben und Protokolle** gezeigt.

## Trainieren eines zweiten Modells für einen Vergleich

Um Algorithmen miteinander zu vergleichen und auszuwerten, welcher besser abschneidet, können Sie in einer Pipeline zwei Modelle trainieren und vergleichen.

1. Kehren Sie zum **Designer** zurück und wählen Sie den Pipelineentwurf **Train-Diabetes-Classifier** aus.
1. Fügen Sie die Komponente **Modell für einen Klassifizierer für logistische Regression trainieren** auf dem Canvas neben der anderen Trainingskomponente hinzu.
1. Verbinden Sie die Ausgabe der Komponente **Numerische Spalten normalisieren** mit der Eingabe der neuen Trainingskomponente.
1. Wählen Sie oben **Konfigurieren & Übermitteln**.
1. Erstellen Sie auf der Seite **Grundlagen** ein neues Experiment mit dem Namen `designer-compare-classification` und führen Sie es aus.
1. Wählen Sie **Überprüfen + Übermitteln** und dann **Übermitteln** aus, um die Pipelineausführung zu starten.
1. Sie können den Status der Ausführung überprüfen, indem Sie zur Seite **Pipelines** wechseln und die Pipeline **Train-Diabetes-Classifier** mit dem **designer-compare-classification**-Experiment auswählen.
1. Warten Sie, bis alle Komponenten erfolgreich abgeschlossen wurden.  
1. Wählen Sie **Auftragsübersicht** und dann die Registerkarte **Metriken** aus, um die Ergebnisse für beide Trainingskomponenten zu überprüfen.
1. Versuchen Sie zu ermitteln, welches Modell besser abgeschnitten hat.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-...** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
