<div align="center">

# 🎋 BambusOs

**Il sistema operativo per router scritto da zero. Bare metal. Nessun compromesso.**

![Linguaggio](https://img.shields.io/badge/linguaggio-C%20%2B%20x86%20ASM-blue?style=flat-square)
![Architettura](https://img.shields.io/badge/arch-x86__64-informational?style=flat-square)
![Stato](https://img.shields.io/badge/stato-Fase%200%20in%20corso-orange?style=flat-square)
![Approccio](https://img.shields.io/badge/approccio-from%20scratch-success?style=flat-square)

</div>

---

## 💡 Cos'è

BambusOs non è un sistema operativo generico che "fa anche il router".
Il kernel **è** il router: un *unikernel di rete* in cui ogni scelta di progetto serve a far
passare pacchetti in modo veloce, prevedibile e sicuro.

Niente Linux, niente BSD, niente framework. Ogni riga, dal bootloader allo stack di rete,
è scritta da zero.

## 🎯 Obiettivi di progetto

| Principio | Cosa significa |
|---|---|
| ⚡ **Data plane a polling** | Niente interrupt né scheduler sul percorso dei pacchetti. Run-to-completion per core, strutture lock-free. |
| 🛡️ **Superficie d'attacco minima** | Nessuna shell, nessun userland Unix, nessun driver superfluo. |
| 🔁 **Config transazionale** | Commit atomico e rollback automatico se qualcosa va storto. |
| 🔭 **Osservabilità nativa** | Contatori, tracing dei pacchetti e telemetria progettati insieme al kernel. |
| 🔄 **Aggiornamenti A/B** | Due immagini e un watchdog: il dispositivo non si brickka. |
| 🚀 **Boot in pochi secondi** | Comportamento deterministico, dall'accensione al primo pacchetto. |

## 🗺️ Roadmap

- [ ] **Fase 0: Fondamenta**: toolchain, boot Multiboot2, output seriale, panic handler, memory map
- [ ] **Fase 1: Kernel minimo**: GDT/IDT, paging, allocatori, heap, timer, APIC
- [ ] **Fase 2: Driver di rete**: PCI, virtio-net, poi NIC Intel reali, ring DMA in polling
- [ ] **Fase 3: Stack L2/L3**: Ethernet, ARP, IPv4, ICMP
- [ ] **Fase 4: Forwarding**: FIB con longest-prefix-match, TTL, frammentazione, route statiche
- [ ] **Fase 5: Servizi base**: UDP, DHCP, DNS forwarder, TCP minimale, NTP
- [ ] **Fase 6: Firewall e NAT**: connection tracking, stateful firewall, NAT44
- [ ] **Fase 7: Control plane**: VLAN, bridging, PPPoE, IPv6, VRRP, RIP, OSPF, BGP
- [ ] **Fase 8: Gestione**: CLI seriale, commit/rollback, API, telemetria
- [ ] **Fase 9: Persistenza e aggiornamenti**: storage, immagini A/B, watchdog
- [ ] **Fase 10: Performance e hardening**: benchmark RFC 2544, fuzzing, fault injection
- [ ] **Fase 11: Hardware reale**: mini-PC con NIC Intel

## 🧰 Requisiti

Ambiente di sviluppo consigliato: **Linux** (oppure **WSL2 + Ubuntu** su Windows).

```bash
sudo apt install build-essential nasm qemu-system-x86 gdb \
                 xorriso grub-pc-bin grub-common mtools git
```

Verifica gli strumenti:

```bash
gcc --version
nasm -v
qemu-system-x86_64 --version
grub-mkrescue --version
```

> 💡 Su WSL2 tieni il repository dentro il filesystem Linux (`~/...`), non in `/mnt/c/...`: è molto più veloce.

## 🚀 Build ed esecuzione

> ⚠️ I target qui sotto sono quelli previsti dalla Fase 0 e sono in corso di sviluppo.

```bash
make build    # compila il kernel
make iso      # crea l'immagine bootabile
make run      # avvia in QEMU con output seriale
make debug    # avvia QEMU in attesa di GDB (-s -S)
```

Per agganciare il debugger:

```bash
gdb build/kernel.elf -ex "target remote :1234" -ex "break kmain"
```

## 📁 Struttura del repository

```
.
├── Makefile
├── linker.ld
├── src/
│   ├── boot/        # assembly di avvio (header Multiboot2, entry point)
│   └── kernel/      # kernel in C
└── build/           # output di compilazione
```

## 🧪 Strategia di test

Ogni fase ha un checkpoint verificabile. Il banco di prova usa:

- **QEMU** con interfacce tap
- **Network namespace** Linux come "vicini" di rete
- **scapy** per generare pacchetti su misura
- **Wireshark / tcpdump** per ispezionare il traffico
- **iperf3 / pktgen** per il carico
- **Test di regressione automatici** a ogni commit

## 📐 Convenzioni di compilazione

Il kernel è compilato in ambiente *freestanding*:

```
-ffreestanding -nostdlib -mno-red-zone -fno-stack-protector -fno-pic -mgeneral-regs-only
```

## 🤝 Filosofia

- **Learning by doing**: si impara scrivendo codice, un passo concreto alla volta.
- **Da zero**: niente codice copiato, niente fork da adattare.
- **Profondità prima della velocità**: ogni fase si chiude solo quando il checkpoint è superato.

---

<div align="center">

*Costruito pacchetto dopo pacchetto.* 📦

</div>
