TinyC 1.6.43 Integration in Tasmota 15.5.0
Ausgangsbasis
Tasmota 15.5.0 (eigener Fork)
Branch: tinyc-test
TinyC-IDE: 1.6.43
Ziel: Firmware und IDE auf ABI v16
Dateien aus Ottelos Repository übernommen
1. xdrv_124_tinyc_vm.h

Pfad:

tasmota/include/xdrv_124_tinyc_vm.h

Komplett ersetzt.

Enthält u.a.

TinyC 1.6.43
ABI v16
#define TC_RELEASE "1.6.43"
#define TC_SYSCALL_ABI 16
2. xdrv_124_tinyc.ino

Pfad

tasmota/tasmota_xdrv_driver/xdrv_124_tinyc.ino

Komplett ersetzt.

Wichtig:

Hier befindet sich u.a.

bool tc_current_is_spawn_worker()

Fehlt diese Datei, entsteht später:

undefined reference to
tc_current_is_spawn_worker()
3. xdrv_124_tinyc_camera.h

Pfad

tasmota/include/xdrv_124_tinyc_camera.h

Komplett ersetzt.

Eigene Anpassungen
TasmotaSerial

Dateien

lib/default/TasmotaSerial-3.7.0/src/TasmotaSerial.h
lib/default/TasmotaSerial-3.7.0/src/TasmotaSerial.cpp

Ergänzt:

Header
#ifdef ESP32
bool setRxFifoFull(uint8_t threshold);
#endif
CPP
#ifdef ESP32
bool TasmotaSerial::setRxFifoFull(uint8_t threshold)
{
    if (!m_hardserial || !TSerial) {
        return false;
    }

    return uart_set_rx_full_threshold(m_uart, threshold) == ESP_OK;
}
#endif

Nicht innerhalb einer anderen Funktion einfügen!

Typische Fehler
Fehler 1
qualified-id in declaration before '(' token

Ursache:

setRxFifoFull() wurde innerhalb von

hardwareSerial()

eingefügt.

Fehler 2
'by' does not name a type

Ursache:

Dateianfang versehentlich

by/*

statt

/*
Fehler 3

IDE

device firmware ABI v2
IDE ABI v16

Ursache

Firmware noch TinyC 1.6.38.

Fehler 4
undefined reference to
tc_current_is_spawn_worker()

Ursache

Neue Header-Datei übernommen,
aber alte

xdrv_124_tinyc.ino

noch vorhanden.

Ergebnis

Nach erfolgreicher Integration:

IDE

Firmware compatible

syscall-ABI v16

device 1.6.43
IDE 1.6.43

Bootlog

TinyC v1.6.43 initialized

Hello-World

Hello TinyC!
...
Done!

läuft fehlerfrei.

Git-Verlauf
master
    |
    +-- TinyC Zwischenstand vor Ottelo 15.5.0
            |
            +-- neue TinyC ergänzt
                   (tinyc-test)
Empfehlung für zukünftige TinyC-Updates

Beim nächsten Update nicht nur einzelne Header kopieren.

Immer diese drei TinyC-Dateien als Paket übernehmen:

tasmota/include/xdrv_124_tinyc_vm.h
tasmota/include/xdrv_124_tinyc_camera.h
tasmota/tasmota_xdrv_driver/xdrv_124_tinyc.ino

Anschließend kompilieren. Falls neue Compiler- oder Linkerfehler auftreten, liegen diese erfahrungsgemäß an Änderungen der Tasmota-Basis (wie bei TasmotaSerial) und können anschließend gezielt angepasst werden.

Ich würde diese Anleitung als TinyC_Update_1.6.43.md direkt im Repository ablegen. Dann hast du bei zukünftigen TinyC-Versionen (1.6.44, 1.6.45 usw.) sofort eine Checkliste und musst nicht mehr rekonstruieren, welche Dateien damals betroffen waren.

wie kann ich die im Repo ablegen

Das ist eine gute Idee. Genau dafür ist Git auch gedacht: Nicht nur den Code, sondern auch die Dokumentation versionieren.

Ich würde im Hauptverzeichnis deines Repositories einen Ordner docs anlegen. Das ist bei vielen Projekten üblich.

Beispiel:

Tasmota/
│
├── docs/
│   ├── TinyC_Update_1.6.43.md
│   ├── ESP32-S3_ETH.md
│   ├── Ottelo_Anpassungen.md
│   └── Backup_Restore.md
│
├── tasmota/
├── lib/
├── tools/
└── ...
In VS Code
Im Explorer rechts auf den Projektordner Tasmota klicken.
Neuer Ordner → docs
Im Ordner docs → Neue Datei

Dateiname:

TinyC_Update_1.6.43.md
Den Text hineinkopieren und speichern.

Markdown (.md) wird von VS Code automatisch formatiert dargestellt. Mit Strg+Shift+V kannst du jederzeit eine Vorschau öffnen.

Anschließend mit Git speichern
git add docs/TinyC_Update_1.6.43.md
git commit -m "Dokumentation TinyC 1.6.43 Integration"

oder einfach wieder über die Source-Control-Seitenleiste von VS Code.

Ich würde noch einen Schritt weiter gehen

Da du inzwischen einige eigene Erweiterungen an Tasmota hast, würde ich die Dokumentation etwas strukturieren:

docs/
│
├── README.md                     <- Übersicht aller Dokumente
├── TinyC_Update_1.6.43.md
├── TinyC_Entwicklung.md
├── Ottelo_Aenderungen.md
├── ESP32-S3_ETH.md
├── Wasserzaehler.md
├── SML_Chart.md
├── Backup_Restore.md
└── Changelog_HaWe.md

Gerade Changelog_HaWe.md könnte sehr nützlich sein. Dort würdest du kurz notieren, was du an deinem Fork geändert hast, zum Beispiel:

# Changelog HaWe

## 2026-07-22
- TinyC auf Version 1.6.43 aktualisiert
- TasmotaSerial um setRxFifoFull() erweitert
- xdrv_124_tinyc_vm.h ersetzt
- xdrv_124_tinyc_camera.h ersetzt
- xdrv_124_tinyc.ino ersetzt

## 2026-07-18
- TinyC-Testbranch erstellt
- ABI-Konflikt untersucht

## 2026-07-10
- Fußzeile im Webinterface angepasst

Ich glaube, das würde sich für dein Projekt langfristig auszahlen. Du entwickelst deinen Fork inzwischen seit Monaten weiter, und mit einer solchen Dokumentation findest du Änderungen auch nach einem halben Jahr noch schnell wieder.