# Urbanisation du SI pour une application 3tiers avec Azure 

![image](https://github.com/user-attachments/assets/b8276b0e-9495-4d68-b82e-584a8305547e)
Creation Groupes de ressources : rg-app3tiers

![image](https://github.com/user-attachments/assets/e3e25cbd-dab1-48b9-964c-8fd74df2854b)
Creation Groupe de sécurité réseau : nsg-app3tiers-backend / nsg-app3tiers-frontend

![image](https://github.com/user-attachments/assets/f9a20886-c269-4749-be82-9c7129705d01)
Creation Réseau Virtuel : vnet-app3tiers, Subnet-Front : 10.0.1.0/24, Subnet-Back : 10.0.2.0/24

![image](https://github.com/user-attachments/assets/bdc20b4a-291f-4b72-a854-39ff6beecbfb)
Creation Machine Virtuel : vm-app3tiers-frontend / vm-app3tiers-backend

Config :

- image 
![image](https://github.com/user-attachments/assets/97cc33b3-d599-4289-9102-9c2e71aca5af)

- taille
![image](https://github.com/user-attachments/assets/85001564-bcfc-475d-9f7d-257a4b87756e)

- Type d'authentification : Mot de passe

- Ports d'entrée publics : frontend : HTTP (80), HTTPS (443), SSH (22), backend : Aucun

- Type de disque de système d'exploitation
![image](https://github.com/user-attachments/assets/d43fe251-fb74-49f9-b45b-883de0e854d1)

- Réseau virtuel : vnet-app3tiers

- Sous-réseau : frontend : Subnet-Front, backend : Subnet-Back

- Adresse IP publique : frontend : Nouveau, backend: Aucun

- Groupe de sécurité réseau de la carte réseau : Aucun 





