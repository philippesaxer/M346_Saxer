# B2
![Instanz-Details](<./img/Screenshot 2026-11-16 085358.png>)
* **Instanz-Name / ID:** KN02 (`i-0d2064d131b8297b6`)
* **Betriebssystem:** Ubuntu Server 26.04 LTS (`ubuntu-resolute-26.04-amd64-server`)
* **Instanz-Typ / CPUs:** `t3.micro` mit 2 vCPUs
* **RAM:** 1 GiB Arbeitsspeicher
* **Diskgrösse:** 8 GiB (EBS gp3 Root-Volume)
* **Zugewiesenes Key Pair:** `philippe_M346_1`[cite: 4, 8, 9, 10]
![SSH-Erfolg](<./img/Screenshot 2026-11-16 094542.png>)
![Ablehnung](<./img/Screenshot 2026-11-16 100840.png>)
![Vergleich](<./img/Screenshot 2026-11-16 101313.png>)

**Erklärung:**  
Der lokal aus `philippe_M346_1.pem` extrahierte öffentliche Schlüssel stimmt mit dem Eintrag in `~/.ssh/authorized_keys` auf dem Server überein. AWS hat diesen öffentlichen Schlüssel beim Erstellen der Instanz automatisch dort hinterlegt, damit der SSH-Dienst beim Verbindungsaufbau prüfen kann ob der Client im Besitz des passenden privaten Schlüssels (`philippe_M346_1.pem`) ist – während `philippe_M346_2.pem` abgelehnt wird, da dessen Public Key auf dem Server nicht autorisiert ist.
