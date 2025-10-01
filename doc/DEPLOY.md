
# Procédure de Déploiement

Décrivez ci-dessous votre procédure de déploiement en détaillant chacune des étapes. De la préparation du VPS à la méthodologie de déploiement continu.

Pour la procédure de déploiement, j'ai utilisé aaPanel.

**Pour commencer :**

Récupérer les identifiants pour ce connecter en SSH sur le serveur.

Exemple: Adresse IP : 172.17.2.56 - Identifiant SSH : root - Mot de passe SSH : XcF895PMsza

Se connecter au serveur :

Ouvrir un terminal et entrer cette commande :

ssh root@172.17.2.56

Et à la demande du mot de passe mettre celui du serveur ssh --> XcF895PMsza

Ensuite, installer aaPanel dans le terminal ou vous êtes connecté en SSH.

Entrer cette commande pour lancer l'installation :

URL=https://www.aapanel.com/script/install_7.0_en.sh&& if [ -f /usr/bin/curl ];then curl -ksSO "$URL";else wget --no-check-certificate -O install_7.0_en.sh "$URL";fi;bashinstall_7.0_en.sh aapanel

Si ça ne fonctionne pas,utiliser sudo

Lorsque l'installation est terminée, récupérer bien vos identifiants ainsi que les deux adresses IP qui vous seront founi.
Exemple :
aaPanel Internet Address: https://90.80.241.65:898989/d82a5698
aaPanel Internal Address: https://172.20.2.89:16006/d82a8956
username: sqplsoqs
password: 0ozie123

Sur votre navigateur diriger vous vers la deuxieme URL

Connectez-vous avec vos identifiant

Et ensuite pour la configuration de l'environnement, choisissez environemment LNMP et cliquez sur One Click install

(LNPM Recommanded - celui sur la gauche)

L'installation va se lancer

Aller dans aaPanel
Aller dans website
Ensuite cliquer sur le bouton Add site
Ajouter votre IP dans dans Domain/IP
Et valider

Sur le terminal ou vous connecter en ssh, faire les commandes suivantes

Configurer le depot git sur le serveur
cd /var

mkdir depot_git

cd depot_git

git init --bare

Sur vscode :

initialiser le git cliff

git cliff --init

Cela crée un fichier cliff.toml pour la configuration

Initialiser le dépôt Git (si pas déjà fait)

bash

git init

gitadd.

git commit -m "feat: initial commit"

Générer le changelog

bash

git cliff --bump -o .\CHANGELOG.md

Configuration SSH (si nécessaire)

Si le push échoue, modifiez la configuration SSH sur le serveur :

cd /etc/ssh

nano sshd_config

Trouvez la ligne PermitRootLogin et modifiez-la.

PermitRootLogin yes

Sauvegardez (Ctrl+X, puis Y, puis Entrée) et redémarrez le service SSH :

systemctl restart sshd

Lier le dépôt distant et déployer

git remote add vps root@172.17.2.56:/var/depot_git

git tag 1.0.0

git push -u vps 1.0.0

Entrez le mot de passe lorsque demandé

Configuration finale dans aaPanel

Créer le fichier .env :

Allez dans File (gestionnaire de fichiers)

Naviguez vers /www/wwwroot/192.168.172.136

Créez un fichier .env

Remplir avec les bonnes données le .env

Configurer le répertoire de travail :

Dans Website, cliquez sur votre site

Allez dans Site directory

Changez Running directory vers /public

Désactivez la protection XSS Attack si nécessaire

Configurer la base de données :

Mettez à jour le fichier .env avec les identifiants de la base de données créée dans aaPanel

Créer un script de déploiement

Retournez dans votre terminal SSH et créez le script :

cd /root

touch deploy.sh

chmod +x deploy.sh

nano deploy.sh

Contenu du script deploy.sh :

if [ -z "$TAG" ]; then
    echo "Usage: ./deploy.sh <tag|branche>"
    exit 1
fi

pour l'utiliser :

./deploy.sh 1.0.0

**Procédure de mise à jour**

**Workflow complet de mise à jour**

Local : Modifications et commit

git add .

git commit -m "fix: correction bug ...."

Local : Génération du changelog

git cliff --bump -o .\CHANGELOG.md

Local : Commit du changelog

gitadd CHANGELOG.md

git commit -m "changelog version `<num TAG>`"

Local : Création du tag

git tag `<num-TAG>`

Local : Push vers le VPS

git push -u vps 1.3.2

Serveur : Déploiement

ssh root@172.17.2.56

./deploy.sh 1.3.2
