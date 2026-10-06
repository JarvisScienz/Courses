# Percorso di studio — Homelab SRE su Fedora

Struttura pensata per sessioni da **~2 ore**, con un mix costante di teoria (30-45 min) e pratica (75-90 min). Le sessioni sono progressive: non passare alla successiva finché quella pratica non è effettivamente funzionante sul tuo PC.

Totale: **29 sessioni** (~58 ore). Procedi al tuo ritmo, una o più sessioni a settimana.

---

## Fase 1 — Basi di sistema e rete (6 sessioni)

| # | Teoria (30-45') | Pratica (75-90') | Risorsa |
|---|---|---|---|
| 1 | systemd: unit, target, dipendenze | Creare 2-3 unit file custom (es. uno script che gira come servizio), gestirli con `systemctl`, leggere log con `journalctl` | `man systemd.unit`, Arch Wiki "systemd" |
| 2 | Utenti, gruppi, permessi, ACL, sudo | Creare utenti/gruppi dedicati al progetto, configurare `sudoers` granulare, testare ACL su una cartella condivisa | `man sudoers`, Red Hat System Admin Guide |
| 3 | Partizionamento, LVM, filesystem | Creare Volume Group e Logical Volume su un disco/immagine, fare resize a caldo, provare ext4 vs xfs | Fedora Docs — Storage Administration |
| 4 | TCP/IP, routing, bridge di rete | Configurare un bridge Linux (`nmcli` o `ip`) che userai poi per le VM | Fedora Networking Guide |
| 5 | Firewall: netfilter/nftables/firewalld | Scrivere regole firewalld per isolare i servizi che costruirai nelle fasi successive | Fedora Docs — Firewalld |
| 6 | DNS: concetti base, zone, record | Installare dnsmasq (o bind), risolvere hostname interni per le tue VM (es. `master.lab`, `worker1.lab`) | dnsmasq man page |

## Fase 2 — Virtualizzazione (3 sessioni)

| # | Teoria | Pratica | Risorsa |
|---|---|---|---|
| 7 | KVM/QEMU/libvirt: architettura | Installare KVM/libvirt, creare la prima VM da CLI con `virt-install` | Fedora Virtualization Guide |
| 8 | Snapshot, clone, gestione risorse | Gestire 2-3 VM via `virsh` (start/stop/snapshot/clone), assegnare CPU/RAM | libvirt docs |
| 9 | Rete tra VM (bridge, NAT interno) | Creare 3 VM (bastion, app, storage) che si vedono tra loro sulla tua rete bridge, verificare con ping/ssh | — |

## Fase 3 — Containerizzazione (3 sessioni)

| # | Teoria | Pratica | Risorsa |
|---|---|---|---|
| 10 | Namespace, cgroups, immagini vs container | Installare Docker su una VM, containerizzare una tua app Node/React esistente | Docker Docs — Get Started |
| 11 | Layer, multi-stage build, registry | Scrivere un Dockerfile multi-stage ottimizzato, montare un registry privato locale | Docker multi-stage build guide |
| 12 | Networking Docker, volumi | `docker-compose` con app + Redis + Postgres collegati, volumi persistenti | Compose file reference |

## Fase 4 — Kubernetes (6 sessioni)

| # | Teoria | Pratica | Risorsa |
|---|---|---|---|
| 13 | Control plane, etcd, kubelet, pod | `kubeadm init` sul nodo master, verificare componenti con `kubectl get pods -n kube-system` | Kubernetes Docs — kubeadm |
| 14 | CNI e networking tra pod (Calico/Flannel) | Join dei worker node, installare CNI, verificare comunicazione pod-to-pod su nodi diversi | Calico/Flannel docs |
| 15 | Deployment, Service, ReplicaSet | Deployare l'app containerizzata (fase 3), esporla con un Service | Kubernetes Concepts |
| 16 | Ingress e reverse proxy | Installare Traefik o nginx-ingress, esporre l'app con un host name reale | Ingress controller docs |
| 17 | Storage persistente (PV/PVC) | Configurare Longhorn o NFS provisioner, montare storage persistente su un pod stateful | Longhorn docs |
| 18 | Troubleshooting: CrashLoopBackOff, OOMKilled, probe | Sessione dedicata a rompere volontariamente pod (limiti risorse errati, probe sbagliate) e debuggarli | `kubectl describe`, `kubectl logs` |

## Fase 5 — Osservabilità (4 sessioni)

| # | Teoria | Pratica | Risorsa |
|---|---|---|---|
| 19 | Metriche, scraping, PromQL base | Installare Prometheus sul cluster, scrivere le prime query PromQL sulle metriche dell'app | Prometheus Docs |
| 20 | Dashboard e visualizzazione | Collegare Grafana a Prometheus, costruire una dashboard reale (CPU, memoria, latenza) | Grafana Docs |
| 21 | Logging centralizzato | Installare Loki + Promtail, centralizzare i log di app e sistema | Loki Docs |
| 22 | SLI/SLO/error budget, alerting | Definire 2-3 SLO reali per la tua app, configurare Alertmanager con notifiche | Google SRE Book — cap. SLO |

## Fase 6 — Infrastructure as Code e automazione (4 sessioni)

| # | Teoria | Pratica | Risorsa |
|---|---|---|---|
| 23 | Terraform: state, provider, plan/apply | Provisioning delle VM via Terraform (provider libvirt) invece che a mano | Terraform Docs — libvirt provider |
| 24 | Ansible: playbook, idempotenza, inventory | Playbook che configura da zero un nodo (utenti, firewall, Docker) | Ansible Docs |
| 25 | Pipeline CI/CD: concetti | Installare GitLab CI runner o Jenkins in un container | Documentazione GitLab CI / Jenkins |
| 26 | Pipeline end-to-end | Pipeline completa: build immagine → test → deploy su K8s ad ogni push | — |

## Fase 7 — Reliability engineering (3 sessioni)

| # | Teoria | Pratica | Risorsa |
|---|---|---|---|
| 27 | Chaos engineering: principi | Chaos Mesh (o kill manuale di pod/nodi), osservare come reagisce il sistema e gli alert | Chaos Mesh Docs, "Chaos Engineering" (O'Reilly) |
| 28 | Runbook e postmortem blameless | Scrivere il primo runbook per un incidente simulato | Google SRE Book — cap. Postmortem |
| 29 | Incident drill a tempo | Simulazione cronometrata: qualcosa si rompe, tu segui il runbook, poi scrivi il postmortem | — |

---

### Come usare questo piano
- Ogni sessione è pensata per stare in ~2 ore anche se sei stanco: se sfori, è normale nelle prime fasi (K8s in particolare).
- Se una sessione pratica non funziona, non passare oltre "per non perdere tempo" — è lì che si impara davvero.
- Tieni un file `NOTES.md` nel repo del progetto con quello che hai imparato/rotto in ogni sessione: diventa la base dei tuoi runbook nella fase 7.