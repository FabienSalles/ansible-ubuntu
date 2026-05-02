# Configuration Chezmoi + Ansible

Guide de mise en place de la gestion des dotfiles avec chezmoi intégré à Ansible.

## Étapes manuelles à réaliser

### 1. Configuration 1Password

Créer les items suivants dans 1Password :

#### Item "Scaleway" (Vault: Private)
| Champ | Valeur |
|-------|--------|
| `secret_key` | `56c58adc-f04a-405b-979f-b3f3e4409622` |
| `access_key` | `SCWC15G91M2JE0H1FR77` |
| `region` | `fr-par` |

#### Item "chezmoi-age-key" (Vault: Private)
| Champ | Valeur |
|-------|--------|
| `key` | Contenu complet de `~/.config/chezmoi/key.txt` |

Pour récupérer la clé age :
```bash
cat ~/.config/chezmoi/key.txt
```

### 2. Créer le repo GitHub dotfiles

```bash
# Se placer dans le repo chezmoi
cd ~/.local/share/chezmoi

# Créer le repo sur GitHub (organisation conveycode)
gh repo create conveycode/dotfiles --private --source . --push

# Vérifier
gh repo view conveycode/dotfiles --web
```

### 3. Commiter les changements Ansible

```bash
cd ~/projects/github/ansible-ubuntu

# Voir les modifications
git status

# Ajouter les nouveaux rôles
git add roles/1password roles/chezmoi ansible-desktop.yml

# Commiter
git commit -m "feat: add chezmoi and 1password roles for dotfiles management"

# Pousser
git push
```

### 4. Tester l'installation 1Password CLI

```bash
# Tester le rôle 1password seul
cd ~/projects/github/ansible-ubuntu
ansible-playbook ansible-desktop.yml --tags "1password" --check

# Si OK, exécuter
ansible-playbook ansible-desktop.yml --tags "1password"

# Se connecter à 1Password
eval $(op signin)

# Vérifier
op vault list
```

### 5. Tester chezmoi

```bash
# Tester le rôle chezmoi seul (dry-run)
ansible-playbook ansible-desktop.yml --tags "chezmoi" --check

# Si OK, exécuter
ansible-playbook ansible-desktop.yml --tags "chezmoi"

# Vérifier que les dotfiles sont appliqués
chezmoi status
chezmoi diff
```

### 6. Test complet sur machine virtuelle (optionnel)

```bash
# Créer une VM Ubuntu avec Vagrant ou Multipass
multipass launch --name test-ansible 22.04

# Copier les clés SSH
multipass transfer ~/.ssh/id_ed25519.pub test-ansible:.ssh/authorized_keys

# Se connecter et tester
multipass shell test-ansible
```

---

## Dépannage

### 1Password non connecté
```bash
# Vérifier le statut
op account list

# Se reconnecter
eval $(op signin)
```

### Erreur de clé age
```bash
# Régénérer une clé age
age-keygen -o ~/.config/chezmoi/key.txt

# Mettre à jour dans 1Password
cat ~/.config/chezmoi/key.txt
# Copier dans 1Password > Private > chezmoi-age-key > key
```

### Chezmoi ne trouve pas le repo
```bash
# Réinitialiser
chezmoi init git@github.com:conveycode/dotfiles.git --force
chezmoi apply
```

---

## Flux de travail quotidien

### Modifier un dotfile
```bash
# Éditer via chezmoi (recommandé)
chezmoi edit ~/.zshrc

# Voir les changements
chezmoi diff

# Appliquer
chezmoi apply

# Commiter
chezmoi cd
git add -A && git commit -m "update zshrc" && git push
```

### Synchroniser sur une autre machine
```bash
chezmoi update
```
