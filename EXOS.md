# Exercices WorkShop RivieraDev

## Préparation

> Normalement, pas besoin de cette partie

Par exemple si vous utilisez Docker, ou si vous n'avez pas paramétré Git

```shell
git config --global user.name "busterbunny69"
git config --global user.email "buster.bunny.69@gmail.com"
git config --global credential.helper "cache --timeout=3600"

# Générer une clé SSH
ssh-keygen -t rsa -C "buster.bunny.69@gmail.com"
# Afficher la clé SSH
cat ~/.ssh/id_rsa.pub

# Ajouter votre clé SSH sur votre compte GitHub
# https://github.com/settings/keys
```

> ⚠️ bien sûr remplacer le user et l'email

## Exercice 1: Création du projet

**Objectif**: Initialiser le projet

### Côté GitHub

- Allez sur votre compte GitHub
- Créez un repository `calculator`  
- Vous le créez en public
- ⚠️ ne pas initialiser de `README`, ni rien d'autre

### Dans votre terminal (ou console)

- cloner ce projet: https://github.com/Rivieradev2017/calc sous un autre nom

```shell
git clone git@github.com:Rivieradev2017/calc.git calculator
cd calculator
# Changer l'url du projet vers votre repository ⚠️ vous n'êtes pas busterbunny69
git remote set-url origin git@github.com:busterbunny69/calculator.git
# check
git remote -v
# puis pour pousser ça chez GitHub
git push -u origin master
```

- aller vérifier côté GitHub que le projet a été publié correctement
- en local, installer les dépendances: `npm install`
- vérifier que les tests du projet fonctionnent: `npm test`
- le lancer
- tester: http://localhost:8080 (ou 9090 si vous êtes avec l'image Docker)
- tester: http://localhost:9090/add/2/40


## Exercice 2: Déployer le projet chez Clever-Cloud

**Objectif**: Déployer le projet


- Aller sur https://www.clever-cloud.com/
- Clicker sur le bouton **SIGN UP AND TRY** (celui avec le logo GitHub)
- renseigner les informations
- > 🚧 créer une Organisation (ou pas?)
- à partir de maintenant vous pouvez créer des applications

### Ajouter une application

- cliquez sur le lien **Add an application**
- déroulez la liste en dessous de **Create an application from a Github repository**
- trouvez votre repository `calculator`
- pour le type d'application choisissez **Node**
- cliquez sur **NEXT**
- vous pouvez changer le nom de l'application et sa zone de déploiement si vous le souhaitez
- cliquez sur **CREATE**
- cliquez sur **I DON'T NEED ANY ADD-ON**
- laissez les variables d'environnement 
- cliquez sur **NEXT**
- le déploiement commence
- allez dans la rubrique **Domain names**
- donnez un nom de domaine à votre application (ex: http://bustercalculator.cleverapps.io/)

⚠️ Si vous allez dans la rubrique **Information**, vous pouvez noter que l'on peut choisir la branche du repository GitHub qui servira à déployer, par défaut `master`

## Exercice 3: déploiement continu

> je code, ça déploie ...

**Objectif**: Ajouter un service au projet et le déployer

### Créer une feature branch

```shell
git checkout -b wip-multiply # par defaut, créée à partir de master
# -b -> create new branch
git branch # affiche la branche courante
```

### Ajouter le service "multiply"

Dans `calc.js` ajouter

```javascript
exports.multiply = (a,b) => a * b;
```

Dans `index.js` ajouter

```javascript
app.get('/multiply/:a/:b', (req, res) => {
  res.send({
    result: calc.multiply(Number(req.params.a), Number(req.params.b))
  });
});
```

Puis, faites:

```shell
git add . # vous "mettez vos modifs en conf"
```

Vérifiez:

```shell
git status

# Vous allez obtenir ceci:
On branch wip-multiply
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   calc.js
        modified:   index.js
```

Committez:

```shell
git commit -m "🐼 a new service"
```

Poussez chez GitHub:

```shell
git push origin wip-multiply
```

### Créez une pull request

- aller sur votre repository gitHub (si nécessaire faite un "refresh")
- vous avez une nouvelle branche apparaît
- et on vous propose de créer une nouvelle "pull request"
- cliquez sur **Compare & New pull request** 
- Décrivez (ou pas) ce que vous proposez
- cliquez sur **Create pull request**
- ⚠️ **Ne mergez pas**, il faut ajouter les tests

### Ajouter des tests

Dans `tests/basic.js` ajouter

```javascript
describe("21 * 2 equals 42", () => {
  let a = 21;
  let b = 2;
  it("should return 42", () => {
    assert.equal(calc.multiply(a, b) , 42)
  });
});
```

Puis:

```shell
git add . 
git commit -m "🦊 add test"
git push origin wip-multiply
```

- Retournez sur votre pull request dans votre repository GitHub
- Et maintenant **mergez** (cliquez sur le bouton **Merge pull request**)

Côté Clever-Cloud, si tout va bien, le merge sur **master** qui est donc la branche de production, est détecté et un re déploiement est détecté.

Vous pouvez aller essayer votre service: http://bustercalculator.cleverapps.io/multiply/2/21

⚠️⚠️⚠️ Donc maintenant vous savez faire du **déploiement continu** 🕺

## Exercice 4: intégration continue

**Objectif**: Demander à Jenkins de lancer mes tests

http://o9mbre4aqm-jenkins.services.clever-cloud.com:4013/

se connecter (avec le user/password) que je vous aurais donné
(ex: `buster01/buster01`)

### Aller sur votre compte GitHub

- sélectionnez **settings**: https://github.com/settings/profile
- sélectionnez **Personal access tokens**
- cliquez sur **Generate new token**
  - donnez un nom à votre token
  - cochez **repo**
  - cliquez sur **Generate token**
  - ⚠️ copiez votre token dans un coin ex: `74d2b2bbcda5e0717ad3a3f28c8116203c8e7369`

### Installer l'intégration Jenkins sur votre repository GitHub

### Paramétrage Jenkins - GitHub

- allez dans les settings de votre repository (https://github.com/busterbunny69/calculator/settings)
- sélectionnez **Intégrations & services**
  - déroulez la liste **Add service**
  - cherchez "jenkins"
  - sélectionnez **Jenkins (GitHub plugin)**
  - donnez l'url du serveur suivie de `github-webhook/` 
    - ⚠️ le `/` à la fin est obligatoire
    - http://o9mbre4aqm-jenkins.services.clever-cloud.com:4013/github-webhook/
  - cliquez sur **Add service**


- Allez dans **Manage Jenkins** > Configure System
  - Add GitHub Server
  - Credentials
    - Add: sélectionnez Jenkins, puis comme type de credential:
      - `Secret text`
      - copiez votre token dans la rubrique `Secret`
      - donnez un `ID` et une `Description`
      - cliquez sur **Add**
    - Sélectionnez la credential que vous avez créée
    - cliquez sur **Test connection** pour vérifier
- cliquez sur **Save**

### Paramétrage Jenkins - NodeJS

- Allez dans **Manage Jenkins** > Global Tool Configuration
  - A la rubrique **NodeJS**
    - cliquez sur **Add NodeJS**
      - donnez un nom à votre installation: `node_install`
      - laissez cochée `Install automatically`
      - sélectionnez la version de node la plus récente
- cliquez sur **Save**

### Créer un Job

- cliquez sur **New Item** ou sur **new jobs**
- donnez un nom à votre item: `busterbunny69` (donnez lui votre handle)
- sélectionnez **GitHub Organization**
- cliquez sur **OK**

- A la rubrique **Projects**
  - vérifier le **Owner** (c'est votre handle GitHub)
  - Au niveau des credentials
    - cliquez sur **Add**
    - sélectionnez ce que vous aviez créé précédement
    - **username**: votre handle GitHub
    - **password**: le token de toute à l'heure (`74d2b2bbcda5e0717ad3a3f28c8116203c8e7369`)
    - donnez un `ID` et une `Description`
    - cliquez sur **Add**  
    - sélectionnez la credential que vous avez créée
- cliquez sur **Save**

### Ajouter un fichier Jenkinsfile à votre projet

Dans votre terminal

```shell
git checkout master
git pull # pour récupérer la dernière version
```

Ajouter un fichier `Jenkinsfile` (sans extention) avec le contenu suivant:

```groovy
node {

    stage 'Checkout'
    checkout scm

    stage 'Build'
    def nodeHome = tool name: 'node_install', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
    env.PATH = "${nodeHome}/bin:${env.PATH}"

    sh "npm install"
    sh "npm test"
}
```

puis:

```shell

git add .
git commit -m "add Jenkinsfile"
git push origin master
```

### Dans Jenkins à la racine de votre job

- vous devez voir apparaître une ligne avec le nom de votre projet
  - si ce n'est pas le cas, cliquez sur **Scan Organization Now**
- et si vous cliquez sur cette ligne
  - vous verrez une ligne avec la branche master

### Retournons sur le projet GitHub

- allez dans les settings du projet
  - https://github.com/busterbunny69/calculator/settings
  - sélectionnez **Branches**
  - à la rubrique **Protected branches**
    - sélectionnez **master**
      - cochez **Protect this branch**
      - cochez **Require status checks to pass before merging**
      - cochez **Require branches to be up to date before merging**
      - cliquez sur **Save changes**

## Exercice 5 - faire une pull request

En fait nous allons modifier directement un test

```shell
git checkout -b wip-my-test
```

Et modifiez un des tests dans `tests/basic.js` pour qu'il soit faux:

```javascript
describe("geek value equals 42", () => {
  let geek = 42;
  it("should return 42", () => {
    assert.equal(geek, 84)
  });
});
```

```shell
git add .
git commit -m "tests"
git push origin wip-my-test
```

Vous pouvez vérifier qu'il y a une nouvelle branche dans votre job Jenkins et que le build est en rouge

Côté GitHub, vous pouvez créer la pull request, et vous allez voir que cela vous affiche que les vérifications sont en erreur

Si vous modifiez votre code pour que le test soit juste et que vous repoussiez votre code, le statut de la pull request va repasser en vert

Si vous mergez, il y aura un re-déploiement

## Exercice 6 - Ajouter un bot à tout ça

**Objectifs**

- déployer un bot sur Clever
- le faire parler dans RocketChat (à chaque déploiement)

J'ai un repository avec le code source d'un hubot: https://github.com/Rivieradev2017/bob

Le plus simple est de le forker (on ne va pas faire de modification dessus)

-  aller sur https://github.com/Rivieradev2017/bob
- cliquez sur **fork** (en haut à droite)

### Déploiement du bot

- aller sur la console d'admin de Clever Cloud
- **Add an application**
- chercher le fork dans vos projets GitHub
- sélectionnez une application **Node**
- cliquez sur **NEXT**
- cliquez sur **CREATE**
- cliquez sur **I DON'T NEED ANY ADD-ON**
- copiez ceci dans les variables d'environnement:

```shell
EXPRESS_PORT=8080
LISTEN_ON_ALL_PUBLIC=true
PORT=8080
ROCKETCHAT_AUTH=password
ROCKETCHAT_PASSWORD=bob
ROCKETCHAT_ROOM=general
ROCKETCHAT_URL=http://rocket.chat.cleverapps.io
ROCKETCHAT_USER=bob
```

- En remplaçant `ROCKETCHAT_PASSWORD=bob` par `ROCKETCHAT_PASSWORD=bob01` (si vous êtes **buster01**)
- En remplaçant `ROCKETCHAT_USER=bob` par `ROCKETCHAT_USER=bob01` (si vous êtes **buster01**)
- cliquez sur **NEXT**

### Nom de domaine

- donnez un nom de domaine à votre bot: par exemple http://bob01.cleverapps.io/

Pendant que votre bot se déploie, dans la console d'administration, allez sélectionner **Organisation Manager**

- puis **Notifications**
- sélectionnez **WEBHOOKS**
- cliquez sur **ADD**
- donnez un nom au hook
- donnez l'url qui va permettre de parler au bot: http://bob01.cleverapps.io/hey/bob (remarque: allez regarder le code de Bob)
- sélectionnez les notifications:
  - **Service activity**
  - **Result of deployments**
- cliquez sur **CREATE**

### Connectez vous au Chat

- allez sur http://rocket.chat.cleverapps.io
- connectez vous en tant que buster01 / buster01 (ou buster02 / buster02, etc...)
- vous allez voir que votre bot a dit "Hello 🌍" au démarrage
- maintenant, re-démarrez votre application **calculator** et observez


Pour plus d'infos sur comment faire un bot: http://k33g.github.io/2017/04/24/DEPLOY-HUBOT-ON-CLEVER-CLOUD.html
