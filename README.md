# Urbanisation du SI pour une application 3tiers avec Azure 

![image](https://github.com/user-attachments/assets/b8276b0e-9495-4d68-b82e-584a8305547e)
Creation Groupes de ressources : rg-app3tiers

![image](https://github.com/user-attachments/assets/e3e25cbd-dab1-48b9-964c-8fd74df2854b)
Creation Groupe de sécurité réseau : nsg-app3tiers-backend / nsg-app3tiers-frontend

Config nsg-app3tiers-backend :

![image](https://github.com/user-attachments/assets/68a42816-eb22-46b0-ad5d-eeee60948f01)
Règles de sécurité de trafic entrant :

- Plages d'adresses IP/CIDR sources : 10.0.1.0/24 (Subnet-Front)
- Ports de destination : 8080 pour l'API et 27017 pour MongoDB ou * pour tout
- Protocole : Any
- Action : Autorisé

![image](https://github.com/user-attachments/assets/f9a20886-c269-4749-be82-9c7129705d01)
Creation Réseau Virtuel : vnet-app3tiers
- Subnet-Front : 10.0.1.0/24 associé Groupe de sécurité nsg-app3tiers-frontend
- Subnet-Back : 10.0.2.0/24 associé Groupe de sécurité nsg-app3tiers-backend

![image](https://github.com/user-attachments/assets/bdc20b4a-291f-4b72-a854-39ff6beecbfb)
Creation Machine Virtuel : vm-app3tiers-frontend / vm-app3tiers-backend

Config :

- image 
![image](https://github.com/user-attachments/assets/97cc33b3-d599-4289-9102-9c2e71aca5af)

- taille
![image](https://github.com/user-attachments/assets/85001564-bcfc-475d-9f7d-257a4b87756e)

- Type d'authentification : Mot de passe
- Nom d’utilisateur : azureadmin
- Mot de passe : P@ssword12345
- Ports d'entrée publics : frontend : HTTP (80), HTTPS (443), SSH (22), backend : Aucun
- Type de disque de système d'exploitation
![image](https://github.com/user-attachments/assets/d43fe251-fb74-49f9-b45b-883de0e854d1)

- Réseau virtuel : vnet-app3tiers
- Sous-réseau : frontend : Subnet-Front, backend : Subnet-Back
- Adresse IP publique : frontend : Nouveau, backend: Aucun
- Groupe de sécurité réseau de la carte réseau : Aucun (car deja associé dans Subnet)

![image](https://github.com/user-attachments/assets/d8fb26a5-8a05-4105-bc6c-b997a745aa2e)
Creation Azure Cosmos DB : 

- Type : MongoDB
- Architecture : Request unit (RU) database account
- Workload Type : Learning
- Groupe de ressources : rg-app3tiers
- Emplacement : (Europe) France Central
- Mode de capacité : Serverless
- Méthode de connectivité : Point de terminaison privé
- Créer un point de terminaison privé : Réseau virtuel : vnet-app3tiers, Sous-réseau : Subnet-Back
- Data Encryption : Service-managed key

![image](https://github.com/user-attachments/assets/53bdcd61-3706-4d01-8cd2-61498a3c5e02)
Installation :

- Connexion : ``` ssh azureadmin@IPpubliqueVMfrontend ``` ![image](https://github.com/user-attachments/assets/cf8cd2d2-5095-4a7b-831b-e4b2ee383626) / Password : P@ssword12345
```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```
- Aller dans /var/www/html et creer un fichier html : ```sudo nano index.html```

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mon Catalogue de Jeux</title>
    <style>
        body { font-family: sans-serif; text-align: center; margin-top: 50px; background-color: #f0f0f0; }
        h1 { color: #333; }
        ul { list-style: none; padding: 0; }
        li { background-color: #fff; margin: 10px auto; padding: 15px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); width: 80%; max-width: 500px; }
    </style>
</head>
<body>
    <h1>Bienvenue sur notre catalogue de jeux !</h1>
    <p>Voici la liste des jeux (chargée depuis l'API) :</p>
    <ul id="games-list">
        <li>Chargement des jeux...</li>
    </ul>
</body>
<script src="script.js"></script> </html>
```

Creer un fichier au meme endroit script.js : ```sudo nano script.js```

```javascript
async function getGames() {
    try {
        const response = await fetch(`/api/games`); // Remplacez /api/games par votre endpoint réel
        if (!response.ok) {
            throw new Error(`Erreur HTTP ! Statut: ${response.status}`);
        }
        const games = await response.json();
        displayGames(games); // Fonction pour afficher les jeux sur la page
    } catch (error) {
        console.error('Erreur lors de la récupération des jeux:', error);
        document.getElementById('games-list').innerHTML = '<li>Impossible de charger les jeux pour le moment.</li>';
    }
}

function displayGames(games) {
    const ul = document.getElementById('games-list');
    ul.innerHTML = ''; // Nettoyer la liste existante
    games.forEach(game => {
        const li = document.createElement('li');
        li.textContent = game.name || game.title; // Assurez-vous que la propriété est correcte (name ou title)
        ul.appendChild(li);
    });
}

// Appeler la fonction au chargement de la page
document.addEventListener('DOMContentLoaded', getGames);
```

- Aller dans /etc/nginx/sites-available et rajouter un reverse proxy pour communiquer avec l'API
```nginx
location /api/ {
                proxy_pass http://10.0.2.4:8080;
                #proxy_pass http://Adresse_IP_privée_VM_Backend:Port_API;
        }
```

![image](https://github.com/user-attachments/assets/91444d11-a8d4-4532-a43e-e5afeb3d6ba5)
Installation :

- Se Connecter dabord a vm-app3tiers-frontend puis : ``` ssh azureadmin@IPprivéVMbackend ``` ![image](https://github.com/user-attachments/assets/7d7401dd-dd09-4019-b23a-8723f7f4fb32)
 / Password : P@ssword12345

```bash
sudo apt update
sudo apt install nodejs npm -y
```
