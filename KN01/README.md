## Fehler 
### Multipass Hash Mismatch Fehler

Beim Versuch, eine Instanz mit Multipass zu starten, habe ich folgenden Fehler erhalten:

```cmd
launch failed: Hash of C:/ProgramData/Multipass/cache/hcs/vault/images/resolute-20261016/ubuntu-26.04-server-cloudimg-amd64.img does not match (expected 5645d0318ad21f643ebb1ac4567514620e9893380b07b0201fda163baa5b7eb5 but got e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855)
```
Ich habe es zuerst mit `multipass launch --name smoke -c 1 -m 1G -d 5G 26.04` versucht.  
Danach habe ich es einfach mit `multipass launch --name demo` probiert.  
Bei mir kam in beiden Fällen exakt das gleiche Resultat. Es hat keinen Unterschied gemacht, welchen Namen oder welche Parameter ich mitgegeben habe.  
Ich habe jetzt eine Lösung gefunden. Ich habe es zuhause heruntergeladen.

## B) Hypervisor-Experiment

### 1. Vergleich Hypervisor Typen
| Person | Betriebssystem | Multipass-Backend (`local.driver`) | Hypervisor-Typ | Begründung |
| :--- | :--- | :--- | :--- | :--- |
| **Philippe** | Windows 11 Home | `hcs` | **Typ 1** (Bare-Metal) | Obwohl es Windows 11 Home ist nutzt das hcs Backend direkt die eingebaute Windows-Hypervisor-Plattform. Der Hypervisor läuft direkt auf der Hardware unterhalb von Windows. |
| **Lennis** | Windows 11 Home | `virtualbox` | **Typ 2** (Hosted) | VirtualBox läuft als klassische Desktop Anwendung auf dem Host Betriebssystem und greift erst über dieses auf die Hardware zu. |

### 2. Host-Ressourcen (Referenzwerte)

* **Mein Host (Philippe):** 
  ![Meine Host-Ressourcen](img/b_host_philippe.png)

* **Host von Lennis**
  ![Host-Ressourcen Kollege](img/b_host_kollege.png)

### 3. Overcommitment-Tests (CPU & RAM)

* **Meine Tests (`hcs`):**
  * **CPU (`-c 20`):** Wurde beim Start mit `create_compute_system failed with 0x80004005` abgelehnt.
  * **RAM (`-m 24G`):** Konnte gestartet werden; `free -h` zeigt ca. 24 GiB an.
  ![Meine Overcommitment-Tests](img/b_tests_philippe.png)

* **Tests von [Name Kollege] (`[sein Backend]`):**
  * **CPU:** [Erfolg oder Fehlermeldung eintragen]
  * **RAM:** [Erfolg oder Fehlermeldung eintragen]
  ![Overcommitment-Tests Kollege](img/b_tests_kollege.png)

### 4. Erklärung (Zeitscheiben & Swap)

* **CPU-Overcommitment (Zeitscheiben):** Ein Hypervisor kann physische CPU-Threads über **Zeitscheiben (Time Slices)** auf mehrere VMs aufteilen, da die Kerne nicht ständig zu 100 % ausgelastet sind. Dass eine *einzelne* VM jedoch mehr virtuelle Prozessoren (`-c 20`) erhält, als physische Threads existieren, wird von `hcs` (Hyper-V) aus Stabilitäts- und Performancegründen blockiert, da eine einzelne VM niemals alle vCPUs gleichzeitig auf echte Hardware-Threads abbilden könnte.
* **RAM-Overcommitment (Swap):** Im Gegensatz zur CPU lässt sich RAM nicht in Zeitscheiben aufteilen. Dass die Zuweisung von 24 GB RAM trotzdem funktioniert hat, liegt an **Dynamic Memory** und **Swap (Paging)**: Solange das Gast-System den Speicher nicht komplett ausfüllt, wird auf dem Host nur der tatsächlich belegte RAM verbraucht. Wird mehr RAM genutzt als physisch frei ist, lagert Windows ungenutzte Speicherseiten (*Pages*) aus dem RAM auf die SSD in die Auslagerungsdatei (`pagefile.sys`) aus – was zwar funktioniert, bei hoher Auslastung aber zu starken Geschwindigkeitseinbussen führt.
