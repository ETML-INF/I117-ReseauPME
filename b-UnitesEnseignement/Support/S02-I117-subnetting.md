---
marp: true
theme: default
paginate: true
footer: "ETML - Module I117 - AGR"
---
# S-117-subnetting – Support sur l'adressage IP , les subnets, classful , classless (CIDR)

**Module :** ETML – Module 117  
**Auteur :** Alexis Gugler  
**Création :** 19.05.2024  
**Version :** 1 du 19.05.2024  
**Durée estimée de lecture :** 45 minutes  

---

## Introduction

Ce support retrace l'évolution de l'adressage IP depuis les débuts d'Internet jusqu'à l'introduction du subnetting et de l'adressage classless (CIDR). L'objectif est de comprendre **pourquoi** et **comment** ces concepts sont apparus, à travers des faits historiques, des limitations concrètes, et les réponses techniques qui ont été apportées.

---

## 1. Aux origines : l'Internet des pionniers

Dans les années 1970, l'ancêtre d'Internet, **ARPANET**, reliait quelques universités et centres de recherche. L'idée d'un réseau global était encore embryonnaire. Les équipements connectés étaient peu nombreux et chaque machine disposait d'une **adresse IP publique unique**, directement routable sur le réseau global. 

Il **n’existait pas de réseaux locaux privés** comme nous les connaissons aujourd’hui. Il n’y avait **pas de NAT**, pas de séparation entre réseaux internes et externes : tout hôte connecté devait avoir une adresse IP unique dans l’espace mondial IPv4. Cela fonctionnait bien… tant que peu de machines étaient connectées.

---

### Faits historiques :
- **1974** : première définition de TCP/IP (RFC 675).
- **1981** : normalisation officielle d’IPv4 (RFC 791).
- Les adresses IP étaient allouées **manuellement** par un registre central géré par le Stanford Research Institute.

---

## 2. Le modèle Classful : une première tentative d'organisation

Rapidement, on se rend compte que donner à chaque organisation une adresse IP individuelle ne suffira pas. L’**espace d’adressage IPv4 est limité à 2^32 adresses**, soit environ 4,3 milliards. En réponse, l’IANA introduit le modèle **classful** (défini en 1981), qui divise l’espace IP en **classes**.

### Répartition en classes

| Classe | Masque | Taille d’un réseau | Destinataires |
|--------|--------|--------------------|----------------|
| A      | /8     | 16,777,216 hôtes   | Très grandes entreprises (IBM, Apple...) |
| B      | /16    | 65,536 hôtes       | Moyennes entreprises / universités |
| C      | /24    | 256 hôtes          | Petites organisations |

---
### Répartition en classes
Chaque classe est définie implicitement par les premiers bits de l’adresse. **Il n’était pas encore question de choisir un masque arbitraire**. Cela a permis une organisation plus simple à l’époque, mais très vite **inefficace**.

### Limites du classful
- Les petites entreprises recevaient des blocs de 256 IP (Classe C), même si elles n’avaient besoin que de 10.
- Les grandes entreprises se voyaient attribuer des millions d’adresses.
- Résultat : **gaspillage massif** de l’espace IPv4.

---

## 3. Le problème de la croissance d'Internet

Pendant les années 1980 et 1990, Internet explose. De plus en plus d’organisations se connectent. Chaque machine connectée doit avoir **une IP publique** :

> Il n’y a pas encore de mécanisme généralisé pour utiliser des IP privées ou faire du NAT.

Cela signifie que même un petit réseau local dans une école ou une PME consomme des adresses **routables mondialement**.

### Conséquences :
- Saturation des classes A/B/C.
- Besoin de créer des sous-réseaux plus fins pour **mieux utiliser les blocs IP**.

---

## 4. L'émergence des réseaux locaux (LAN)

C’est dans ce contexte que les **réseaux locaux** apparaissent. On commence à connecter plusieurs machines sur une même infrastructure Ethernet dans une organisation. Ces machines ont besoin de communiquer entre elles **sans sortir sur Internet**.

Pour cela, il faut pouvoir :
1. Identifier localement chaque machine (via IP).
2. Gérer la communication interne (ARP, broadcast).
3. Séparer les communications locales des flux vers Internet.

Mais le problème persiste : **toutes ces machines ont besoin d’une IP publique**, car les routeurs d’Internet ne savent pas gérer autre chose.

---

## 5. La réponse partielle : le subnetting (découpage en sous-réseaux)

Pour économiser les adresses, on commence à **découper un bloc IP classful** en plusieurs **sous-réseaux plus petits**. C’est ce qu’on appelle le **subnetting**.

> Le subnetting permet de définir **un masque de sous-réseau plus précis** que celui imposé par la classe.

---

### Exemple :
Une entreprise possède un réseau de classe B `172.16.0.0/16`. Elle peut le découper en :
- `172.16.1.0/24` pour le service IT
- `172.16.2.0/24` pour les RH
- etc.

Ainsi, chaque département dispose d’un sous-réseau isolé, tout en restant dans la plage initiale. Cela limite le broadcast et améliore la sécurité et la performance réseau.

---

## 6. L'adressage privé et le NAT

Dans les années 1990, deux avancées majeures vont changer la donne : 
**IP privées & NAT**
### RFC 1918 (1996) – IP privées
Introduction des plages IP **réservées à un usage local** :

- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

Ces adresses **ne sont pas routables sur Internet**. Elles permettent aux organisations de créer de vastes réseaux internes **sans consommer d’IP publiques**.

---

### NAT (Network Address Translation)
Le **NAT** permet de faire correspondre plusieurs adresses internes à une seule adresse IP publique. Une entreprise peut ainsi avoir :
- 200 postes en IP privées
- 1 seule IP publique pour sortir sur Internet

Le NAT répond à la crise imminente de pénurie d’adresses, mais complexifie le routage et casse certains protocoles (comme SIP).

---

## 7. CIDR (Classless Inter-Domain Routing)

Face à la saturation et à l’inefficacité du modèle classful, une évolution radicale est proposée en 1993 : le **CIDR** (RFC 1518 & 1519).

### Objectifs :
- Abandonner le modèle basé sur les classes
- Permettre des **masques de réseau arbitraires** (VLSM)
- Réduire la taille des tables de routage dans les routeurs Internet (agrégation de routes)

---

### Exemple CIDR :
- Adresse IP : `192.168.12.0`
- Masque : `/27` (au lieu de `/24` imposé par la classe C)
- Cela donne 32 adresses au lieu de 256, mieux adaptées à de petits besoins

CIDR permet aussi à un fournisseur d’accès de recevoir un bloc `/19` et de l’allouer dynamiquement à ses clients sous forme de `/24`, `/28`, etc.

---

## 8. Récapitulatif de l’évolution

| Époque | Organisation | Problèmes | Solutions |
|--------|--------------|-----------|-----------|
| 1970s | IP publiques directes | Peu de machines | Pas de séparation LAN/WAN |
| 1980s | Classful (A/B/C) | Gaspillage massif | Modèle rigide |
| 1990s | Subnetting + IP privées + NAT | Pénurie proche | Optimisation locale |
| 1993+ | CIDR / VLSM | Adressage flexible | Routage scalable, plus efficace |


---

## 9. L'arrivée de l'IPv6 : l'avenir de l'adressage IP

### Historique et contexte

Dès les années 1990, il devient évident que l’espace d’adressage IPv4 ne suffira pas à long terme. Malgré l’introduction du subnetting, du NAT et du CIDR, la croissance explosive d’Internet, l’essor des appareils mobiles et l’Internet des objets (IoT) menacent d’épuiser rapidement les adresses IPv4.

- **1994** : Lancement des travaux sur un nouveau protocole d’adressage, d’abord appelé IPng (IP next generation).
- **1998** : Publication officielle de l’IPv6 (RFC 2460).

---

### Caractéristiques de l’IPv6
- **Adresse sur 128 bits** (contre 32 bits pour IPv4), soit 3,4 x 10^38 adresses possibles.
- Nouvelle notation hexadécimale, exemple : `2001:0db8:85a3:0000:0000:8a2e:0370:7334`.
- Intégration native de la sécurité (IPsec), de l’auto-configuration, et d’une gestion plus efficace du multicast.
- Suppression du concept de classes et du NAT : chaque appareil peut avoir une adresse publique unique.

---

### Pourquoi l’IPv6 ?
- **Pénurie d’adresses IPv4** : L’IPv6 a été conçu pour répondre à la croissance exponentielle du nombre d’appareils connectés.
- **Simplification du routage** : Les adresses sont hiérarchisées pour faciliter l’agrégation des routes.
- **Nouvelles fonctionnalités** : Meilleure gestion de la mobilité, de la sécurité et de la configuration automatique.

---

### Adoption
- L’adoption de l’IPv6 est progressive : de nombreux réseaux fonctionnent en double pile (IPv4 + IPv6).
- L’IPv4 reste encore très présent, mais l’IPv6 est indispensable pour l’avenir d’Internet.

---

## Conclusion

L'évolution de l'adressage IP reflète les défis croissants d’un réseau mondial. Passer de quelques dizaines de machines connectées à des milliards a nécessité :

- Une meilleure segmentation du réseau (subnetting)
- Une séparation LAN/WAN (IP privées, NAT)
- Une allocation d’adresses flexible (CIDR)

Ces évolutions ont permis de prolonger la vie d’IPv4 pendant des décennies, malgré sa nature finie. Elles sont à la base de toute conception réseau moderne.
