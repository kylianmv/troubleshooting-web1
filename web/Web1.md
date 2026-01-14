# Rapport d'Intervention : Rétablissement des Services Intranet

**Auteur :** Kylian Mostin-Vanderplanck  
**Date :** 14/01/2026  
**Sujet :** Indisponibilité du site intranet `www.woodytoys.lab` (Web Exercice 1)  
**Machines concernées :** `dhcp`, `resolver`, `soa`, `www`, `directeur`

---

## 1. Schéma du Labo
![Schéma](Webpage.png)

---

## 2. Description de l'Incident

### Symptômes observés
Depuis le poste client `directeur`, l'accès au site web interne est impossible.
* **Test effectué :** Tentative de connexion via le navigateur en ligne de commande `links`.
* **Résultat :** Échec de la connexion (soit "Host not found" si le DNS est éteint, soit "Connection refused" si le serveur Web est éteint).

### Contexte
Le réseau semble fonctionnel physiquement, mais aucun service applicatif (résolution de noms, attribution d'IP, hébergement web) ne répond. L'infrastructure semble être dans un état d'arrêt complet.

---

## 3. Analyse Technique

Le diagnostic a révélé que les démons (services) essentiels n'étaient pas démarrés sur les différents serveurs de l'infrastructure :

1.  **Infrastructure Réseau :** Les serveurs DHCP et DNS (`resolver` et `soa`) étaient inactifs, empêchant les clients d'obtenir une IP et de résoudre le nom de domaine `woodytoys.lab`.
2.  **Service Web :** Le serveur HTTP Apache2 sur la machine `www` était à l'arrêt.

---

## 4. Solution Mise en Œuvre

Pour rétablir le service, une procédure de démarrage séquentiel des services a été appliquée.

### Étape 1 : Démarrage de l'infrastructure réseau (DHCP & DNS)
Lancement des services sur les serveurs d'infrastructure pour rétablir la connectivité et la résolution de noms.

* **Sur la machine `dhcp` :**
    ```bash
    systemctl start isc-dhcp-server
    ```

* **Sur les machines `resolver` et `soa` :**
    ```bash
    systemctl start bind9
    ```

### Étape 2 : Démarrage du serveur Web
Lancement du serveur Apache pour servir la page intranet.

* **Sur la machine `www` :**
    ```bash
    systemctl start apache2
    ```

---

## 5. Validation

Une fois les services redémarrés, un test de validation a été effectué depuis le client `directeur` pour confirmer le bon fonctionnement de la chaîne complète (DHCP -> DNS -> Web).

* **Commande de test :**
    ```bash
    links "http://www.woodytoys.lab"
    ```

* **Résultat obtenu :**
    La page d'accueil s'affiche correctement avec le message "Bienvenue!".

![Preuve de fonctionnement](fonctionne.png)


Le service intranet est désormais pleinement opérationnel.
