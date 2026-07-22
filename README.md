# 🌐 Segmentation du réseau avec les VLAN — OPNsense

> Six VLAN isolant chacun un domaine fonctionnel, avec routage et filtrage inter-VLAN assurés par OPNsense.

## Pourquoi segmenter

Limiter la surface d'attaque, cloisonner les flux entre domaines fonctionnels et simplifier le diagnostic réseau — plutôt qu'un unique réseau plat où tout hôte peut potentiellement en atteindre un autre.

## Cartographie des VLAN

| VLAN | Tag | Plage CIDR | Vocation | Hôtes représentatifs |
|---|---|---|---|---|
| LAN | natif | 192.168.6.0/24 | Administration & cœur | Hyperviseurs, AD, OPNsense, poste de gestion |
| SRV | 20 | — | Serveurs applicatifs | Filers DFS, GLPI |
| GES | 30 | — | Postes de gestion | Postes administrateurs déportés |
| SEC | 40 | — | Sécurité & supervision | Wazuh, Zabbix |
| PRA | 50 | — | Plan de reprise | Proxmox Backup Server |
| VPN | 60 | — | Accès distant | Tailscale et nœuds VPN |

## Procédure de déploiement côté OPNsense

1. Rattacher le VLAN à l'interface physique LAN → génère une sous-interface logique.
2. Assigner une IP à cette sous-interface (= passerelle du sous-réseau).
3. Déclarer le scope DHCP correspondant (voir `opnsense-dhcp-kea`).

## Filtrage inter-VLAN — exemple SRV → LAN (flux Active Directory)

Politique de moindre privilège : tout est refusé et journalisé par défaut.

| Protocole | Port destination | Service Microsoft |
|---|---|---|
| TCP | 88 | Kerberos |
| TCP | 464 | Kerberos password change |
| TCP | 3268 / 3269 | Global Catalog LDAP / LDAPS |
| TCP | 9389 | AD Web Services |
| TCP/UDP | 389 / 636 | LDAP / LDAPS |
| TCP | 445 | SMB |
| TCP | 135 + 49152-65535 | RPC dynamique |
| UDP | 53 | DNS |

## Repos liés

- `opnsense-dhcp-kea`
- [`proxmox-commutateurs-virtuels`](https://github.com/L-VSIX/proxmox-commutateurs-virtuels)

## Auteur

**Lilian Vertueux** — [LinkedIn](https://www.linkedin.com/in/lilian-vertueux/)
