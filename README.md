# TP1_INFO001

## ABRANTES ALFREDO Gabriel
## MESSAOUD-DJEBARA Ziyad

# Compte rendu

## 2. Préparation

### Question 1 :

Pour chiffrer un message M avec RSA et assurer sa confidentialité, on applique la formule C = M^e mod n. Le message en clair M est transformé en message chiffré C en utilisant l'exposant public e et le modulus n. Ces deux paramètres (e, n) forment la clé publique.

Pour déchiffrer, on fait l'opération inverse avec M = C^d mod n. On utilise cette fois l'exposant privé d avec le modulus n, qui constituent la clé privée. Seul celui qui possède cette clé privée peut retrouver le message original.

### Question 2 :

Diffie-Hellman sert à générer une clé secrète commune entre deux personnes (Alice et Bob) même si elles communiquent sur un canal non sécurisé où un attaquant (Eve) peut écouter tous les échanges.

L'idée n'est pas de chiffrer directement les données, mais de créer une clé partagée que seuls Alice et Bob connaîtront. Cette clé sera ensuite utilisée pour du chiffrement symétrique. Même si Eve observe tous les messages échangés, elle ne pourra pas retrouver cette clé secrète.

### Question 3 :

Un certificat contient plusieurs informations importantes :

1. **Le sujet (Subject)** : c'est l'identité du propriétaire du certificat, avec son nom de domaine, son organisation et sa localisation.

2. **La clé publique** : c'est la clé publique du propriétaire, accompagnée de l'algorithme utilisé pour la cryptographie.

3. **Les dates de validité** : elles indiquent la période pendant laquelle le certificat peut être utilisé (date de début et date de fin).

4. **La signature de l'autorité de certification** : c'est la signature de la CA qui a délivré le certificat et qui garantit son authenticité.

On trouve aussi d'autres informations comme l'émetteur du certificat (Issuer), le numéro de série et l'algorithme de signature utilisé.

### Question 4 :

Quand Bob se connecte en HTTPS au site www.alice.com, voici comment se déroule l'authentification :

**Étape 1 - Réception des certificats :**
Bob reçoit le certificat d'Alice ainsi que celui de l'autorité intermédiaire ca1.

**Étape 2 - Vérification du certificat de ca1 :**
Bob commence par vérifier le certificat de ca1. Il déchiffre la signature de ce certificat avec la clé publique de root-ca (qu'il connaît déjà car elle est incluse dans son système d'exploitation ou son navigateur). Il calcule ensuite le hash du certificat et compare les deux hash pour vérifier que tout est correct. Il vérifie aussi que les dates de validité sont bonnes.

**Étape 3 - Vérification du certificat d'Alice :**
Maintenant que Bob fait confiance à ca1, il peut vérifier le certificat d'Alice. Il déchiffre la signature du certificat d'Alice avec la clé publique de ca1, puis calcule et compare le hash pour s'assurer de l'intégrité. Il vérifie que le nom de domaine "www.alice.com" correspond bien à ce qui est écrit dans le certificat (champ Subject ou Subject Alternative Name). Il contrôle aussi les dates de validité et peut éventuellement contacter ca1 pour voir si le certificat n'a pas été révoqué.

**Étape 4 - Preuve de possession de la clé privée :**
Enfin, Alice doit prouver qu'elle possède vraiment la clé privée qui correspond à la clé publique du certificat. Pour ça, elle signe les messages échangés pendant la procédure Diffie-Hellman avec sa clé privée. Bob vérifie cette signature avec la clé publique d'Alice qui est dans le certificat. Si tout est bon, l'authentification est réussie.

## 4. Étude du chiffrement RSA

### 4.1 Génération de clés RSA

### Question 5 :

Le modulus n a une longueur de 1024 bits. Pour chiffrer : C = M^e mod n. Pour déchiffrer : M = C^d mod n. Le publicExponent vaut 65537, une valeur standard facilement devinable. Ce n'est pas problématique car la sécurité de RSA repose sur l'impossibilité de retrouver d sans connaître la factorisation de n.

### Question 6 :

Il n'y a aucun intérêt à chiffrer une clé publique puisqu'elle est faite pour être partagée. En revanche, chiffrer la clé privée est essentiel pour la protéger en cas d'accès non autorisé à la machine.

### Question 7 :

L'encodage utilisé est le format PEM (Privacy Enhanced Mail) en Base64. L'avantage est qu'il utilise uniquement des caractères ASCII imprimables, ce qui permet de copier-coller facilement les clés sans risque de corruption.

### Question 8 :

On retrouve bien le modulus (n) et l'exposant public (e), sans l'exposant privé d. Disposer d'un fichier séparé pour la clé publique permet de la partager facilement sans risquer d'exposer la clé privée, et de gérer différemment les droits d'accès.

### 4.2 Chiffrement asymétrique

### Question 9 :

Pour envoyer un message de manière confidentielle avec RSA, il faut utiliser la clé publique du destinataire pour chiffrer le message.

### Question 10 :

La commande pour chiffrer le fichier clair.txt avec la clé publique du voisin est :

```bash
openssl pkeyutl -encrypt -inkey pub.abranteg.pem -pubin -in clair.txt -out cipher.bin -pkeyopt rsa_padding_mode:oaep
```

Lors de la première tentative, on a rencontré une erreur "data too large for key size" car le message initial était trop grand. Avec une clé RSA de 1024 bits et le padding OAEP, on ne peut chiffrer qu'environ 86 octets maximum. On a donc réduit le contenu de clair.txt à un message plus court : "Bonjour de ...".

### Question 11 :

Après avoir chiffré plusieurs fois le même fichier clair.txt, on constate que les fichiers cipher.bin.1 et cipher.bin.2 ont des contenus différents. C'est le comportement attendu. Le padding OAEP (Optimal Asymmetric Encryption Padding) ajoute de l'aléatoire à chaque chiffrement, ce qui fait que le même message chiffré deux fois donne des résultats différents. C'est grace a cela que l'attaquant ne peux pas reconnaître qu'un même message a été envoyé plusieurs fois.

