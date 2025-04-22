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

## Trainieren eines Modells mithilfe von AutoML

Um die Nutzung der Daten- und anderen Ressourcen im Arbeitsbereich von Azure Machine Learning zu erkunden, lassen Sie uns versuchen, ein Modell zu trainieren.

Eine schnelle Möglichkeit, das beste Modell für eine Aufgabe auf der Grundlage Ihrer Daten zu trainieren und zu finden, ist die Verwendung der Option **AutoML**.

> **Hinweis**: Es werden ggf. Popupelemente angezeigt, um Sie durch das Studio zu leiten. Sie können alle Popupelemente schließen und ignorieren und sich auf die Anweisungen dieses Labs konzentrieren.

1. Laden Sie die Schulungsdaten, die verwendet werden sollen, unter `https://github.com/MicrosoftLearning/mslearn-azure-ml/raw/refs/heads/main/Labs/02/diabetes-data.zip` herunter und entpacken Sie die komprimierten Dateien.
1. Zurück im Azure Machine Learning Studio, wählen Sie die Seite **AutoML** aus dem Menü auf der linken Seite des Studios.
1. Wählen Sie **+ Neuer automatisierter ML-Auftrag**.
1. Legen Sie im Schritt **Grundeinstellungen** einen eindeutigen Namen für Ihren Trainingsauftrag fest und experimentieren Sie oder verwenden Sie die zugewiesenen Standardwerte. Wählen Sie **Weiter** aus.
1. Im Schritt **Aufgabentyp & Daten** wählen Sie **Klassifizierung** als Aufgabentyp und wählen Sie **+Erstellen**, um Ihre Schulungsdaten hinzuzufügen.
2. Auf der Seite **Datenressource erstellen** geben Sie im Schritt **Datentyp** einen Namen für Ihr Daten-Asset an (z. B. `training-data`) und wählen **Weiter**.
1. Im Schritt **Datenquelle** wählen Sie **Aus lokalen Dateien**, um die zuvor heruntergeladenen Schulungsdaten hochzuladen. Wählen Sie **Weiter** aus.
1. Überprüfen Sie im Schritt **Zielspeichertyp**, dass **Azure Blob Storage** als Typ des Datenspeichers ausgewählt ist und dass **workspaceblobstore** der ausgewählte Datenspeicher ist. Wählen Sie **Weiter** aus.
1. Wählen Sie im Schritt **MLTable-Auswahl** die Option **Ordner hochladen** und wählen Sie den Ordner, den Sie aus der zuvor heruntergeladenen komprimierten Datei extrahiert haben. Wählen Sie **Weiter** aus.
1. Überprüfen Sie die Einstellungen für Ihre Datenressource und wählen Sie **Erstellen**.
1. Zurück im Schritt **Aufgabentyp & Daten**, wählen Sie die Daten, die Sie gerade hochgeladen haben, und wählen Sie **Weiter**.

> **TIPP**: Möglicherweise müssen Sie den Typ **Klassifizierung** erneut auswählen, bevor Sie zum nächsten Schritt übergehen.

1. Im Schritt **Aufgabeneinstellungen** wählen Sie **Diabetisch (Boolesch) ** als Zielspalte und öffnen dann die Option **Zusätzliche Konfigurationseinstellungen konfigurieren**.
1. Ändern Sie im Bereich **Zusätzliche Konfiguration** die primäre Metrik in **Genauigkeit**, und wählen Sie dann **Speichern**.
1. Erweitern Sie die Option **Grenzen** und legen Sie die folgenden Eigenschaften fest:
    * **Max. Testversuche**: 10
    * **Zeitüberschreitung des Experiments (Minuten)**: 60
    * **Zeitüberschreitung der Wiederholung (Minuten)**: 15
    * **Vorzeitige Beendigung aktivieren**: Ausgewählt

1. Wählen Sie für **Testdaten** den **Train-Test-Split** und überprüfen Sie, dass der **Prozentsatz der Testdaten** 10 beträgt. Wählen Sie **Weiter** aus.
1. Überprüfen Sie im Schritt **Compute**, dass der Computetyp **Serveless** und die Größe des virtuellen Computers **Standard-DS3-v2** ausgewählt ist. Wählen Sie **Weiter** aus.

> **Hinweis:** Compute-Instanzen und Computecluster basieren auf Standardimages virtueller Azure-Computer. Für diese Übung wird das Image *Standard_DS3_v2* empfohlen, um ein optimales Verhältnis zwischen Kosten und Leistung zu erreichen. Wenn Ihr Abonnement über ein Kontingent verfügt, das dieses Image nicht enthält, wählen Sie ein alternatives Image aus. Beachten Sie jedoch, dass ein größeres Image höhere Kosten verursachen kann und ein kleineres Image möglicherweise nicht ausreicht, um die Aufgaben auszuführen. Bitten Sie alternativ Ihren Azure-Administrator, Ihr Kontingent zu erhöhen.

1. Überprüfen Sie alle Ihre Einstellungen und wählen Sie **Schulungsauftrag senden**.

## Anzeigen des Verlaufs mithilfe von Aufträgen

Nachdem Sie den Auftrag gesendet haben, werden Sie auf die Seite des Auftrags weitergeleitet. Mithilfe von Aufträgen können Sie die von Ihnen ausgeführten Workloads nachverfolgen und miteinander vergleichen. Aufträge gehören zu einem **Experiment**, mit dem Sie Auftragsausführungen gruppieren können. 

1. Beachten Sie, dass Sie in den Parametern **Übersicht** unter anderem den Status des Auftrags finden, wer ihn erstellt hat, wann er erstellt wurde und wie lange seine Ausführung gedauert hat.
1. Es sollte 10-20 Minuten dauern, bis das Training beendet ist. Wenn er abgeschlossen ist, können Sie auch die Details jedes einzelnen Komponentenlaufs, einschließlich der Ausgabe, ansehen. Schauen Sie sich die Jobseite an, um zu verstehen, wie die Modelle trainiert werden.

    Azure Machine Learning erfasst automatisch die Eigenschaften Ihres Auftrags. Mithilfe von Aufträgen können Sie ihren Verlauf einfach einsehen, um zu verstehen, was Sie oder Ihre Kollegen bereits erledigt haben.
    Während des Experimentierens helfen Aufträge, die verschiedenen Modelle nachzuverfolgen, die Sie trainieren, um Modelle zu vergleichen und das beste zu bestimmen. Während der Produktion können Sie mithilfe von Aufträgen überprüfen, ob automatisierte Workloads wie erwartet ausgeführt wurden.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure Machine Learning fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

1. Schließen Sie die Registerkarte „Azure Machine Learning Studio“, und kehren Sie zum Azure-Portal zurück.
1. Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressource erstellen** aus.
1. Wählen Sie die Ressourcengruppe **rg-dp100-labs** aus.
1. Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
