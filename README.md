# LINUX-COMMANDES-DE-RECHERCHES

# Recherche de fichiers et de contenus sous Linux

Ce dépôt regroupe des notes et des exemples pratiques sur les commandes de recherche Linux :  
`find`, `grep`, `locate`, `which` et `whereis`.  
Ces outils sont indispensables pour un administrateur système ou un utilisateur avancé.

---

## 1. Commande `find` – Recherche de fichiers

La commande `find` recherche des **fichiers ou répertoires** selon des critères comme nom, taille, type, date, propriétaire, permissions, etc.

### Syntaxe de base

```bash
find <chemin> <critères> [options]
```

- `chemin` : où commencer la recherche (`/`, `~`, `.`).
- `critères` : conditions sur le fichier (nom, taille, date, type, etc.).

---

### Chemins fréquents

| Chemin | Signification | Exemple |
|--------|---------------|---------|
| `.`    | Répertoire courant | `find . -name "*.txt"` |
| `~`    | Répertoire personnel de l’utilisateur | `find ~ -type f` |
| `/`    | Racine du système (toute l’arborescence) | `find /var/log -size +100M` |

---

### Critères principaux avec `find`

| Critère | Exemple | Explication |
|---------|---------|-------------|
| `-name` | `find . -name "*.log"` | Cherche les fichiers dont le nom correspond au motif (majuscules prises en compte). |
| `-iname` | `find . -iname "*.log"` | Comme `-name`, mais **insensible à la casse**. |
| `-type f` | `find . -type f` | Ne retourne que les **fichiers** (pas les répertoires ni les liens). |
| `-type d` | `find . -type d` | Ne retourne que les **répertoires**. |
| `-size +100M` | `find . -size +100M` | Fichiers **plus grands que 100 Mo**. |
| `-size -50M` | `find . -size -50M` | Fichiers **plus petits que 50 Mo**. |
| `-empty` | `find . -type f -empty` | Fichiers **vides** (taille nulle). |
| `-mtime -7` | `find . -mtime -7` | Fichiers **modifiés dans les 7 derniers jours**. |
| `-user root` | `find . -user root` | Fichiers **appartenant à l’utilisateur root**. |
| `-perm 755` | `find . -perm 755` | Fichiers avec permissions exactes `755`. |

---

### Combiner plusieurs critères

Les critères sont combinés avec un **`-and`** implicite :

```bash
find . -type f -name "*.txt" -mtime -7
```

Pour un **OU** explicite, on utilise `-o` entre parenthèses :

```bash
find . \( -name "*.txt" -o -name "*.log" \) -type f
```

Pour un **NON** :

```bash
find . -type f ! -name "*.bak"
```

---

### Limiter la profondeur de recherche

| Option | Exemple | Explication |
|--------|---------|-------------|
| `-maxdepth N` | `find . -maxdepth 1 -name "*.sh"` | Recherche **jusqu’à N** niveaux de profondeur (ici 1). |
| `-mindepth N` | `find . -mindepth 2 -maxdepth 3` | Ne retourne que les éléments **entre N et M** niveaux de profondeur. |

---

## 2. Commande `grep` – Recherche dans le contenu

`grep` recherche une **chaîne de caractères ou un motif** dans le contenu des fichiers (pas dans les métadonnées).

### Syntaxe simple

```bash
grep "motif" fichier.txt
grep "motif" *.log
grep "motif" /var/log/*.log
```

---

### Options courantes de `grep`

| Option | Exemple | Explication |
|--------|---------|-------------|
| `-i`   | `grep -i "erreur"` | **Insensible à la casse** (trouve `Erreur`, `ERREUR`, etc.). |
| `-v`   | `grep -v "debug"`  | Affiche les lignes qui **ne contiennent pas** le motif. |
| `-n`   | `grep -n "error" log.txt` | Affiche les **numéros de ligne**. |
| `-l`   | `grep -l "TODO" *.sh` | Affiche seulement les **noms de fichiers** (pas les lignes). |
| `-c`   | `grep -c "warning" log.txt` | Compte le **nombre de lignes** correspondantes. |
| `-r`   | `grep -r "TODO" .` | Recherche **récursive** dans tous les sous‑dossiers. |

---

### Exemples combinant `find` et `grep`

| Exemple | Explication |
|---------|-------------|
| `find . -type f -name "*.php" -exec grep -n "config" {} \;` | Cherche tous les `.php` et affiche les lignes contenant `config` avec numéro de ligne. |
| `find /var/log -type f -name "*.log" -exec grep -i "error" {} \; \| less` | Affiche page par page les lignes contenant `error` dans les logs. |
| `find /var/log -type f -name "*.log" -exec grep -c "Warning" {} \; \| awk '{s+=$1} END {print s}'` | Compte le **nombre total** de lignes contenant `Warning` dans tous les logs. |

---

## 3. Autres commandes utiles

### `locate` – Recherche rapide par nom

| Exemple | Explication |
|---------|-------------|
| `locate "*.log"` | Liste rapidement tous les fichiers dont le nom contient `.log`. |
| `locate --regex ".*\.conf$"` | Utilise une expression régulière pour trouver les fichiers se terminant par `.conf`. |

> ⚠️ `locate` utilise une base de données indexée ; mets‑la à jour avec `sudo updatedb` si nécessaire.

---

### `which` et `whereis`

| Commande | Exemple | Explication |
|----------|---------|-------------|
| `which`  | `which python3` | Affiche le **chemin** de l’exécutable trouvé dans le `PATH`. |
| `whereis`| `whereis nginx` | Donne les emplacements de l’exécutable, des pages de manuel, etc. |

---

## 4. Bonnes pratiques

- Utilise toujours des **exemples courants** bien documentés dans tes notes.
- Teste les commandes dangereuses (`find` avec `-exec rm`, recherche sur `/` avec `-user root`, etc.) sur un **dossier de test**.
- Combine `find` / `grep` avec `wc`, `sort`, `uniq`, `head`, `tail` et `less` pour analyser de gros volumes de fichiers.
- Met à jour la base `locate` régulièrement si tu l’utilises souvent (`sudo updatedb`).




## 5. Commande `wc` – Compter lignes, mots et caractères

La commande `wc` (word count) sert à **compter le nombre de lignes, de mots et de caractères** dans un fichier ou dans une entrée de commande (souvent via un pipe `|`).  
Elle est très utile pour **compter le nombre de fichiers trouvés** par `find` ou pour analyser rapidement un fichier de texte.

### Tableau récapitulatif de `wc`

| Option | Exemple | Explication |
|--------|---------|-------------|
| `wc` (sans option) | `wc fichier.txt` | Affiche **lignes**, **mots** et **caractères** pour le fichier. |
| `-l` | `wc -l fichier.txt` | Compte **uniquement le nombre de lignes** (ex. nombre de fichiers listés). |
| `-w` | `wc -w fichier.txt` | Compte **uniquement le nombre de mots**. |
| `-c` | `wc -c fichier.txt` | Compte **le nombre de caractères** (ou d’octets). |
| `-m` | `wc -m fichier.txt` | Compte le nombre de **caractères Unicode** (utile si UTF‑8). |

### Exemples concrets

| Exemple | Effet |
|---------|-------|
| `wc /etc/passwd` | Affiche : nombre de lignes, mots et caractères du fichier `/etc/passwd`. |
| `wc -l /etc/passwd` | Affiche seulement le **nombre de lignes** (ex. 100 lignes). |
| `ls -1 | wc -l` | Compte le **nombre de fichiers/dossiers** dans le répertoire courant (un par ligne). |
| `find / -user root -perm 666 2>/dev/null | wc -l` | Compte le **nombre de fichiers** appartenant à `root` avec permissions `666`. |

### À retenir

- `wc` est souvent utilisée en **combinaison avec `find`, `grep`, `ls`** via `|` pour **compter des résultats**.  
- `wc -l` est l’option la plus utile pour les **TPs** : elle donne directement le **nombre d’éléments** (chaque ligne = un élément).  
- Si tu veux seulement une **valeur chiffrée** (sans le nom du fichier), tu peux parfois combiner avec `awk` :  
  `find ... | wc -l | awk '{print $1}'`.
---


## 5. Critères de filtre et commandes de recherche

Les commandes de recherche Linux se combinent souvent avec des **filtres** pour afficher, compter ou limiter les résultats.  
Les principales commandes de filtrage sont : `tail`, `head`, `wc`, `grep`, `sort`, `uniq`.

### Tableau récapitulatif des filtres courants

| Commande / filtre | Exemple | Effet |
|-------------------|---------|-------|
| `tail`            | `tail -n 5 fichier.txt` | Affiche les **5 dernières lignes** du fichier. |
| `tail -n +5`      | `tail -n +5 fichier.txt` | Affiche **à partir de la 5ᵉ ligne** (exclut les 4 premières). |
| `head`            | `head -n 5 fichier.txt` | Affiche les **5 premières lignes** du fichier. |
| `head -n -5`      | `head -n -5 fichier.txt` | Affiche toutes les lignes **sauf les 5 dernières**. |
| `wc`              | `wc fichier.txt` | Affiche **lignes / mots / caractères** du fichier. |
| `wc -l`           | `ls -1 \| wc -l` | **Compte le nombre de lignes** (ex. nombre de fichiers listés). |
| `wc -w`           | `wc -w fichier.txt` | Compte le **nombre de mots**. |
| `wc -c`           | `wc -c fichier.txt` | Compte le **nombre de caractères** (octets). |
| `grep`            | `grep "motif" fichier.txt` | Affiche les **lignes contenant le motif**. |
| `grep -i`         | `grep -i "erreur" /var/log/*.log` | Idem mais **insensible à la casse**. |
| `grep -v`         | `grep -v "debug" log.txt` | Affiche les lignes **qui ne contiennent pas** le motif. |
| `grep -c`         | `grep -c "warning" log.txt` | **Compte** le nombre de lignes contenant le motif. |
| `grep -l`         | `grep -l "TODO" *.sh` | Affiche **seulement les noms de fichiers** correspondants. |
| `grep -r`         | `grep -r "config" .` | Recherche **récursive** dans tous les sous‑dossiers. |
| `sort`            | `sort fichier.txt` | Trie les lignes **par ordre alphabétique**. |
| `sort -r`         | `find /etc -type f \| sort -r` | Trie en **ordre inverse**. |
| `uniq`            | `sort fichier.txt \| uniq` | Supprime les **lignes dupliquées consécutives**. |
| `uniq -c`         | `sort fichier.txt \| uniq -c` | Compte les occurrences de chaque ligne. |

---

### Exemples de combinaisons utiles

| Exemple | Effet |
|---------|-------|
| `tail -n 5 /etc/passwd` | Affiche les 5 dernières lignes de `/etc/passwd` (nouveaux utilisateurs). |
| `find / -type f -name "*.log" \| grep -i "error" \| wc -l` | Compte le nombre de lignes contenant "error" dans tous les fichiers `.log`. |
| `ls -1 \| head -n 10` | Affiche les 10 premiers fichiers/dossiers du répertoire courant. |
| `cat /var/log/syslog \| tail -n 20 \| grep "warning"` | Affiche les 20 dernières lignes de `syslog`, puis filtre seulement les lignes avec "warning". |

---

### À retenir

- `tail` et `head` servent à **limiter la sortie** (début ou fin).  
- `grep` filtre le **contenu** des lignes.  
- `wc` permet de **compter** les lignes, mots ou caractères.  
- Les commandes sont souvent combinées avec un **pipe** (`|`) pour créer des **filtres en chaîne**.
> **Projet :** 420-FBD-LI – Soutien technique et formation  
> **Étudiant :** [Ton nom] – Cégep de Limoilou
