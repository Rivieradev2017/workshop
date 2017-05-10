# Pré-requis pour le workshop RivieraDev

> 20 personnes max
> Niveau débutant

## Pré-requis obligatoires

- avoir git
- avoir un compte GitHub
- avoir paramétré ses clés SSH avec son compte GitHub
- avoir nodejs (on peut faire sans, mais bon...) -> sinon lire la dernière partie du document

nous n'avons qu'une heure, donc si on prend le temps de créer les comptes GitHub, on n'y arrivera pas

## Nice to have

> cela permettra de gagner encore plus de temps

- s'enregistrer (créer un compte chez Clever-Cloud)

## on va parler de quoi?

6 exercices sont prévus (si on peut faire les 5 premiers cela serait top):

- création d'un projet sur votre poste et chez GitHub
- déployer le projet chez Clever-Cloud
- modification du projet et déploiement continu (sans les mains avec Clever Cloud)
- intégration continue avec Jenkins
- impact de Jenkins sur une pull request
- ajouter un bot

J'ai tout documenté, donc même si on ne va pas au bout, vous pourrez continuer chez vous

## Si vous vous refusez à installer node et git ...

... mais que vous n'avez pas de soucis à utiliser Docker (ou vous pouvez aussi le faire dans une VM)

Vous pouvez tenter de vous faire une image avec le Dockerfile suivant: (⚠️ ne le faites pas pendant le workshop, ça ruinerait la connexion):

```shell
FROM ubuntu:latest

RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# Install Java + some tools
RUN apt-get update && \
    apt-get install -y maven && \
    apt-get install -y git && \
    apt-get install -y git bash-completion && \
    apt-get install -y nano

# Install Node.js
RUN apt-get install -y curl && \
    curl -sL https://deb.nodesource.com/setup_7.x | bash && \
    apt-get install -y nodejs

RUN apt-get clean

WORKDIR /workspace

# Define default command.
CMD ["bash"]
```

- créez un répertoire de travail avec le Dockerfile à l'intérieur
- créez un sous répertoire `workspace`
- buildez l'image: `docker build -t dev-console .;`
- lancez: `docker run -v $(pwd)/workspace:/workspace -p 9090:8080 --name dev-container-hello -i -t dev-console;`
- stoppez: `docker stop dev-container-hello`
- ré-utilisez: `docker start dev-container-hello` puis `docker attach dev-container-hello`

Une fois dans votre terminal avec votre container démarré:

```shell
# paramétrez votre compte GitHub
git config --global user.name "votre-handle-github"
git config --global user.email "votre@e-mail"
git config --global credential.helper "cache --timeout=3600"

# Générer une clé SSH
ssh-keygen -t rsa -C "votre@e-mail"
# Afficher la clé SSH
cat ~/.ssh/id_rsa.pub

# Ajouter votre clé SSH sur votre compte GitHub
# https://github.com/settings/keys
```

> ⚠️ je ne fais pas de support Docker (parce que je ne suis pas bon en Docker)
