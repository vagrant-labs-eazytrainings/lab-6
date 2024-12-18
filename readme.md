# Lab 6 : Création d'un environnement multi-machines avec le plugin vagrant-hostsupdater

## 🌟 Objectif

Ce laboratoire vise à :

1. Créer un environnement **multi-machines** avec Vagrant.
2. Utiliser le plugin **vagrant-hostsupdater** pour gérer automatiquement les entrées dans le fichier **`/etc/hosts`**, permettant de résoudre facilement les adresses IP des machines virtuelles.

Malgré le fait que le plugin **vagrant-hostsupdater** ne soit plus maintenu, il reste **parfaitement fonctionnel** et suffisant pour cet usage.

---

## 📊 Prérequis

- **Vagrant** installé sur votre machine.
- **VirtualBox** comme fournisseur.
- Connexion Internet fonctionnelle.
- Accès administrateur (pour modifier le fichier **`/etc/hosts`**).

---

## 🚀 Étapes du Lab

### 1. Installer le plugin vagrant-hostsupdater

Installez le plugin en utilisant la commande suivante :

```bash
vagrant plugin install vagrant-hostsupdater
```

Vérifiez qu'il est installé avec :

```bash
vagrant plugin list
```

Vous devriez voir une sortie similaire à ceci :

```plaintext
vagrant-hostsupdater (1.2.4, global)
```

---

### 2. Configuration du Vagrantfile

Utilisez le Vagrantfile suivant pour créer un environnement multi-machines avec un **Load Balancer** et deux **Web Servers** :

```ruby
# Variables globales
CPU = 1
RAM = "1024"
OS = "ubuntu/jammy64"
NUM_MACHINES = 2

Vagrant.configure("2") do |config|
  # Configuration globale du provider
  config.vm.provider "virtualbox" do |vb|
    vb.memory = RAM
    vb.cpus = CPU
  end

  # Load Balancer
  config.vm.define "lb" do |lb|
    lb.vm.box = OS
    lb.vm.network "private_network", ip: "10.0.0.10"
    lb.vm.hostname = "lb.local"
    lb.hostsupdater.aliases = ["lb.example"]
  end

  # Serveurs Web (boucle dynamique)
  (1..NUM_MACHINES).each do |i|
    config.vm.define "web#{i}" do |web|
      web.vm.box = OS
      web.vm.network "private_network", ip: "10.0.0.#{10 + i}"
      web.vm.hostname = "web#{i}.local"
      web.hostsupdater.aliases = ["web#{i}.example"]
    end
  end
end
```

---

### 3. Déploiement des machines

1. **Lancez les machines** :
   ```bash
   vagrant up
   ```

2. **Vérifiez les entrées dans `/etc/hosts`** :
   Sur votre machine hôte, exécutez :
   ```bash
   cat /etc/hosts
   ```

   Vous devriez voir des lignes comme :
   ```plaintext
   10.0.0.10 lb.local lb.example
   10.0.0.11 web1.local web1.example
   10.0.0.12 web2.local web2.example
   ```

3. **Tester la connectivité** :
   Depuis votre machine hôte, utilisez les noms d'hôtes ou les alias pour tester la connectivité :
   ```bash
   ping lb.local
   ping web1.local
   ping web2.local
   ```

---

## 🔧 Commandes Utiles

| Commande                        | Description                              |
|---------------------------------|------------------------------------------|
| `vagrant plugin install vagrant-hostsupdater` | Installe le plugin vagrant-hostsupdater. |
| `vagrant plugin list`           | Liste les plugins installés.             |
| `vagrant up`                    | Démarre toutes les machines.            |
| `vagrant ssh <nom_machine>`     | Accède à une machine via SSH.           |
| `vagrant halt`                  | Arrête toutes les machines.             |
| `vagrant destroy`               | Supprime toutes les machines.           |

---

## 📈 Résultat Attendu

1. Les machines virtuelles sont configurées avec des IP privées statiques :
   - **lb** : 10.0.0.10
   - **web1** : 10.0.0.11
   - **web2** : 10.0.0.12

2. Les noms d'hôtes et alias sont résolus via le fichier **`/etc/hosts`**.

3. Vous pouvez accéder à chaque machine en utilisant son nom d'hôte ou son alias.

---

## 🌟 Conclusion

Ce laboratoire a permis de :

1. Configurer un environnement **multi-machines** avec Vagrant.
2. Utiliser le plugin **vagrant-hostsupdater** pour gérer les noms d'hôtes automatiquement.
3. Rendre les machines facilement accessibles via des alias dans **`/etc/hosts`**.

Bien que le plugin **vagrant-hostsupdater** ne soit plus maintenu, il reste parfaitement fonctionnel pour des besoins de développement local. 🚀

Félicitations pour la réussite de ce lab ! 😄
