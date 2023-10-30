---
lab:
  title: Erkunden des Azure Machine Learning-Arbeitsbereichs
---

# Erkunden des Azure Machine Learning-Arbeitsbereichs

Azure Machine Learning bietet eine Data Science-Plattform zum Trainieren und Verwalten von Machine Learning-Modellen. In diesem Lab erstellen Sie einen Azure Machine Learning-Arbeitsbereich und erkunden die verschiedenen Möglichkeiten zum Arbeiten mit dem Arbeitsbereich. Das Lab ist als Einführung in die verschiedenen Kernfunktionen von Azure Machine Learning und die Entwicklertools konzipiert. Wenn Sie sich ausführlicher über die Funktionen informieren möchten, gibt es weitere Labs, die Sie erkunden können.

## Vorbereitung

Sie benötigen ein [Azure-Abonnement](https://azure.microsoft.com/free?azure-portal=true), in dem Sie Administratorzugriff besitzen.

## Bereitstellung eines Azure Machine Learning-Arbeitsbereichs

Ein Azure Machine Learning **-Arbeitsbereich** ist eine Zentrale zum Verwalten aller Daten- und anderen Ressourcen, die Sie zum Trainieren und Verwalten Ihrer Modelle benötigen. Sie können einen Arbeitsbereich über die interaktive Benutzeroberfläche im Azure-Portal oder die Azure CLI mit der Azure Machine Learning-Erweiterung bereitstellen. In den meisten Produktionsszenarien empfiehlt es sich, die Bereitstellung mit der CLI zu automatisieren, damit Sie die Ressourcenbereitstellung in einen wiederholbaren *DevOps-Prozess* (Development/Operations, Entwicklung/Betrieb) integrieren können. 

In dieser Übung stellen Sie Azure Machine Learning über das Azure-Portal bereit und erkunden alle Optionen.

1. Melden Sie sich bei `https://portal.azure.com/` an.
2. Erstellen Sie eine neue **Azure Machine Learning**-Ressource mit den folgenden Einstellungen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: `rg-dp100-labs`
    - **Arbeitsbereichsname**: `mlw-dp100-labs`
    - **Region:***Wählen Sie die nächstgelegene geografische Region aus.*
    - **Speicherkonto:***Für Ihren Arbeitsbereich wird standardmäßig ein neues Speicherkonto erstellt.*
    - **Schlüsseltresor:***Für Ihren Arbeitsbereich wird standardmäßig ein neuer Schlüsseltresor erstellt.*
    - **Application Insights:***Für Ihren Arbeitsbereich wird standardmäßig eine neue Application Insights-Ressource erstellt.*
    - **Containerregistrierung:** Keine (*wird automatisch erstellt, wenn Sie das erste Mal ein Modell in einem Container bereitstellen*)
3. Warten Sie, bis der Arbeitsbereich und die zugehörigen Ressourcen erstellt wurden, was in der Regel etwa 5 Minuten dauert.

> **Hinweis**: Beim Erstellen eines Azure Machine Learning-Arbeitsbereichs können Sie einige erweiterte Optionen verwenden, um den Zugriff über einen *privaten Endpunkt* einzuschränken und benutzerdefinierte Schlüssel für die Datenverschlüsselung anzugeben. Diese Optionen werden in dieser Übung nicht verwendet, aber Sie sollten wissen, dass es sie gibt.

## Erkunden von Azure Machine Learning Studio

*Azure Machine Learning Studio* ist ein webbasiertes Portal, über das Sie auf den Azure Machine Learning-Arbeitsbereich zugreifen können. Sie können in Azure Machine Learning Studio alle Datenbestände und Ressourcen in Ihrem Arbeitsbereich verwalten.

1. Wechseln Sie zur Ressourcengruppe mit dem Namen **rg-dp100-labs**.
1. Vergewissern Sie sich, dass die Ressourcengruppe Ihren Azure Machine Learning-Arbeitsbereich, eine Application Insights-Instanz, eine Key Vault-Instanz und ein Speicherkonto enthält.
1. Wählen Sie Ihren Azure Machine Learning-Arbeitsbereich aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Studio starten** aus. In Ihrem Browser wird eine weitere Registerkarte geöffnet, auf der Azure Machine Learning Studio geöffnet wird.
1. Schließen Sie alle Popupelemente, die in Studio angezeigt werden.
1. Beachten Sie die verschiedenen Seiten, die in Studio links gezeigt werden. Wenn nur die Symbole im Menü angezeigt werden, wählen Sie das Symbol &#9776; aus, um das Menü aufzuklappen und die Namen der Seiten zu erkunden.
1. Beachten Sie den Abschnitt **Autor*in** mit den Optionen **Notebooks**, **Automatisiertes ML** und **Designer*in**. Dies sind die drei Möglichkeiten zum Erstellen eigener Machine Learning-Modelle in Azure Machine Learning Studio.
1. Beachten Sie den Abschnitt **Ressourcen** mit u. a. den Optionen **Daten**, **Aufträge** und **Modelle**. Ressourcen werden beim Trainieren oder Bewerten eines Modells genutzt oder erstellt. Ressourcen dienen zum Trainieren, Bereitstellen und Verwalten Ihrer Modelle und können zum Nachverfolgen des Verlaufs mit Versionen versehen werden.
1. Beachten Sie den Abschnitt **Verwalten** mit u. a. der Option **Compute**. Dies sind Infrastrukturressourcen, die zum Trainieren oder Bereitstellen eines Machine Learning-Modells benötigt werden.

## Erstellen einer Trainingspipeline

Um die Nutzung der Daten- und anderen Ressourcen im Arbeitsbereich von Azure Machine Learning zu erkunden, lassen Sie uns versuchen, ein Modell zu trainieren.

Eine schnelle Möglichkeit zum Erstellen einer Modelltrainingspipeline ist der **Designer**.

> **Hinweis**: Es werden ggf. Popupelemente angezeigt, um Sie durch das Studio zu leiten. Sie können alle Popupelemente schließen und ignorieren und sich auf die Anweisungen dieses Labs konzentrieren.

1. Wählen Sie in Studio links im Menü die Seite **Designer** aus.
1. Wählen Sie das Beispiel **Regression – Automobile Price Prediction (Basic)** aus.

    Eine neue Pipeline wird angezeigt. Oben in der Pipeline wird eine Komponente zum Laden von **Automobile price data (raw)** angezeigt. Die Pipeline verarbeitet die Daten und trainiert ein lineares Regressionsmodell, um den Preis für jedes Fahrzeug vorherzusagen.
1. Wählen Sie oben auf der Seite **Konfigurieren und Übermitteln** aus, um das Dialogfeld **Pipelineauftrag einrichten** zu öffnen.
1. Wählen Sie auf der Seite **Grundlagen** die Option **Neu erstellen** aus, und legen Sie den Namen des Experiments auf `train-regression-designer` fest. Wählen Sie dann **Weiter** aus.
1. Wählen Sie auf der Seite **Eingaben und Ausgaben** die Option **Weiter** aus, ohne Änderungen vorzunehmen.
1. Auf der Seite **Laufzeiteinstellungen** wird ein Fehler angezeigt, da Sie nicht über eine Standardcomputeressource für die Ausführung der Pipeline verfügen.

Erstellen wir nun ein Computeziel.

## Erstellen eines Computeziels

Zum Ausführen einer Workload im Azure Machine Learning-Arbeitsbereich benötigen Sie eine Computeressource. Einer der Vorteile von Azure Machine Learning ist die Möglichkeit zum Erstellen cloudbasierter Computeressourcen, auf denen Sie Experimente und Trainingsskripts im gewünschten Umfang ausführen können.

1. Wählen Sie in Azure Machine Learning Studio links im Menü die Seite **Compute** aus. Sie können vier Arten von Computeressourcen nutzen:
    - **Compute-Instanz**: ein von Azure Machine Learning verwalteter virtueller Computer. Ideal für die Entwicklung, wenn Sie Daten erkunden und mit Machine Learning-Modellen iterativ experimentieren.
    - **Computecluster**: Skalierbare VM-Cluster für die bedarfsgesteuerte Verarbeitung von Experimentcode Ideal für die Ausführung von Produktionscode oder automatisierten Aufträgen.
    - **Kubernetes-Cluster**: Ein Kubernetes-Cluster, der zum Trainieren und Bewerten verwendet wird. Ideal für die Bereitstellung von Modellen in Echtzeit und im großen Stil.
    - **Angefügte Computeressource**: Fügen Sie Ihre vorhandenen Azure-Computeressourcen an den Arbeitsbereich an, z. B. Virtual Machines- oder Azure Databricks-Cluster.

    Zum Trainieren eines mit dem Designer erstellten Machine Learning-Modells können Sie entweder eine Compute-Instanz oder einen Computecluster verwenden.

2. Fügen Sie auf der Registerkarte **Compute-Instanzen** eine neue Compute-Instanz mit den folgenden Einstellungen hinzu. 
    - **Computename**: *Geben Sie einen eindeutigen Namen ein.*
    - **Speicherort**: *automatisch derselbe wie für Ihren Arbeitsbereich*
    - **VM-Typ**: `CPU`
    - **VM-Größe:** : `Standard_DS11_v2`
    - **Verfügbares Kontingent**: zeigt die verfügbaren dedizierten Kerne.
    - **Erweiterte Einstellungen anzeigen**: Beachten Sie die folgenden Einstellungen, aber wählen Sie sie nicht aus:
        - **SSH-Zugriff aktivieren**: `Unselected` *(Sie können über diese Option den Direktzugriff auf die VM mithilfe eines SSH-Clients aktivieren.)*
        - **Virtuelles Netzwerk aktivieren**: `Unselected` *(Diese Option wird in der Regel in einer Unternehmensumgebung verwendet, um die Netzwerksicherheit zu verbessern.)*
        - **Einem anderen Benutzer zuweisen**: `Unselected` *(Sie können mit dieser Option eine Compute-Instanz einem Data Scientist zuweisen.)*
        - **Mit Setupskript bereitstellen**: Nicht ausgewählt `Unselected` *(Sie können mit dieser Option um ein Skript hinzufügen, das auf der Remote-Instanz nach ihrer Erstellung ausgeführt werden soll.)*
        - **Weisen Sie eine verwaltete Identität hinzu**: `Unselected` *(Sie können systemseitig oder benutzerseitig zugewiesene verwaltete Identitäten anfügen, um Zugriff auf Ressourcen zu gewähren.)*

3. Wählen Sie **Erstellen** aus. Warten Sie, bis die Compute-Instanz gestartet wurde und sich der Status in **Wird ausgeführt** geändert hat.

> **Hinweis:** Compute-Instanzen und Computecluster basieren auf Standardimages virtueller Azure-Computer. Für diese Übung wird das Image *Standard_DS11_v2* empfohlen, um ein optimales Gleichgewicht zwischen Kosten und Leistung zu erzielen. Wenn Ihr Abonnement über ein Kontingent verfügt, das dieses Image nicht enthält, wählen Sie ein alternatives Image aus. Beachten Sie jedoch, dass ein größeres Image höhere Kosten verursachen kann und ein kleineres Image möglicherweise nicht ausreicht, um die Aufgaben auszuführen. Bitten Sie alternativ Ihren Azure-Administrator, Ihr Kontingent zu erhöhen.

## Ausführen Ihrer Trainingspipeline

Sie haben ein Computeziel erstellt und können nun Ihre Beispieltrainingspipeline im Designer ausführen.

1. Wechseln Sie zur Seite **Designer**.
1. Wählen Sie den Pipelineentwurf **Regression – Automobile Price Prediction (Basic)** aus.
1. Wählen Sie oben auf der Seite **Konfigurieren und Übermitteln** aus, um das Dialogfeld **Pipelineauftrag einrichten** zu öffnen.
1. Wählen Sie auf der Seite **Grundlagen** die Option **Neu erstellen** aus, und legen Sie den Namen des Experiments auf `train-regression-designer` fest. Wählen Sie dann **Weiter** aus.
1. Wählen Sie auf der Seite **Eingaben und Ausgaben** die Option **Weiter** aus, ohne Änderungen vorzunehmen.
1. Wählen Sie in den **Laufzeiteinstellungen** in der Dropdownliste **Computetyp auswählen** die Option *Compute-Instanz* und in der Dropdownliste **Azure ML-Compute-Instanz auswählen** Ihre neu erstellte Compute-Instanz aus.
1. Wählen Sie **Überprüfen und übermitteln** aus, um den Pipelineauftrag zu überprüfen, und wählen Sie dann **Übermitteln** aus, um die Trainingspipeline auszuführen.

Die Trainingspipeline wird nun an die Compute-Instanz übermittelt. Es dauert ungefähr 10 Minuten, bis die Pipeline abgeschlossen ist. Lassen Sie uns in der Zwischenzeit einige andere Seiten erkunden.

## Anzeigen des Verlaufs mithilfe von Aufträgen

Jedes Mal, wenn Sie im Azure Machine Learning-Arbeitsbereich ein Skript oder eine Pipeline ausführen, wird dieser Vorgang als **Auftrag** aufgezeichnet. Mithilfe von Aufträgen können Sie die von Ihnen ausgeführten Workloads nachverfolgen und miteinander vergleichen. Aufträge gehören zu einem **Experiment**, mit dem Sie Auftragsausführungen gruppieren können.

1. Navigieren Sie in Azure Machine Learning Studio links im Menü zur Seite **Aufträge**.
1. Wählen Sie das Experiment **train-regression-designer** aus, um dessen Auftragsausführungen einzusehen. Hier sehen Sie eine Übersicht aller Aufträge, die Teil dieses Experiments sind. Wenn Sie mehrere Trainingspipelines ausgeführt haben, können Sie in dieser Ansicht die Pipelines vergleichen und die beste bestimmen.
1. Wählen Sie im Experiment **train-regression-designer** den letzten Auftrag aus.
1. Beachten Sie, dass die Trainingspipeline gezeigt wird, in der Sie erkennen können, welche Komponenten erfolgreich ausgeführt wurden und welche nicht. Wenn der Auftrag noch ausgeführt wird, können Sie auch ermitteln, was gerade ausgeführt wird.
1. Um die Details des Pipelineauftrags anzuzeigen, wählen Sie rechts oben **Auftragsübersicht** aus, die Übersicht **Pipelineaufträge** aufzuklappen.
1. Beachten Sie, dass Sie in den Parametern unter **Übersicht** u. a. den Status des Auftrags, den Ersteller der Pipeline, den Zeitpunkt ihrer Erstellung und die Dauer der Ausführung der gesamten Pipeline finden können.

    Wenn Sie ein Skript oder eine Pipeline als Auftrag ausführen, können Sie Eingaben festlegen und Ausgaben dokumentieren. Azure Machine Learning verfolgt auch automatisch die Eigenschaften Ihres Auftrags nach. Mithilfe von Aufträgen können Sie ihren Verlauf einfach einsehen, um zu verstehen, was Sie oder Ihre Kollegen bereits erledigt haben.

    Während des Experimentierens helfen Aufträge, die verschiedenen Modelle nachzuverfolgen, die Sie trainieren, um Modelle zu vergleichen und das beste zu bestimmen. Während der Produktion können Sie mithilfe von Aufträgen überprüfen, ob automatisierte Workloads wie erwartet ausgeführt wurden.

1. Wenn Ihr Auftrag abgeschlossen ist, können Sie auch die Details jeder einzelnen Komponentenausführung einsehen, einschließlich der Ausgabe. Sie können die Pipeline nach Wunsch erkunden, um zu verstehen, wie das Modell trainiert wird.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-labs** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
