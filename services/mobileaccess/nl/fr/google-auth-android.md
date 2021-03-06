---

copyright:
  years: 2015, 2016
lastupdated: "2016-10-10"
---
{:screen: .screen}
{:shortdesc: .shortdesc}

# Activation de l'authentification Google pour les applications Android
{: #google-auth-android}

Utilisez Google pour authentifier les utilisateurs sur votre application Android {{site.data.keyword.amafull}}. Ajoutez une fonctionnalité de sécurité {{site.data.keyword.amashort}}. 

## Avant de commencer
{: #before-you-begin}
Vous devez disposer des éléments suivants :

* Un projet Android dans Android Studio qui est configuré pour fonctionner avec Gradle. Il n'a pas besoin d'être instrumenté avec un SDK client de {{site.data.keyword.amashort}}.  
* Une instance d'une application {{site.data.keyword.Bluemix_notm}} qui est protégée par le service {{site.data.keyword.amashort}}. Pour plus d'informations sur la création d'un système de back end {{site.data.keyword.Bluemix_notm}}, voir [Initiation](index.html).
* Vos valeurs de paramètres de service. Ouvrez votre service dans le tableau de bord {{site.data.keyword.Bluemix_notm}}. Cliquez sur **Options pour application mobile**. Les valeurs `applicationRoute` et `tenantId` (qui portent également le nom d'`appGUID`) s'affichent dans les zones **Route** et **Identificateur global unique de l'application / ID titulaire**. Vous aurez besoin de ces valeurs pour l'initialisation du logiciel SDK et l'envoi de demandes à l'application de back-end.

La configuration de l'authentification Google pour votre application Android {{site.data.keyword.amashort}} nécessite également la configuration de :
* L'application {{site.data.keyword.Bluemix_notm}}
* Votre projet Android Studio

## Création d'un projet sur Google Developer Console
{: #create-google-project}

Pour commencer à utiliser Google en tant que fournisseur d'identité, vous devez créer un projet dans [Google Developer Console](https://console.developers.google.com). 
Une partie de la création d'une projet consiste à obtenir un ID client Google.  L'ID client Google est un identificateur unique pour votre application utilisé par
l'authentification Google et nécessaire pour configurer l'application {{site.data.keyword.Bluemix_notm}}.

Depuis la console :

1. Créez un projet en utilisant l'API **Google+**.
2. Ajoutez l'accès utilisateur **OAuth**.
3. Avant d'ajouter les données d'identification, vous devez choisir la plateforme (Android).
4. Ajoutez les données d'identification. 

Pour terminer la procédure de création des données d'identification, vous devez ajouter l'**empreinte digitale du certificat signataire**.



### Configuration du certificat signataire
Pour que Google puisse vérifier l'authenticité de votre applications, vous devez spécifier l'empreinte digitale du certificat signataire.

Sur le système d'exploitation Android, toutes les applications installées sur un périphérique Android doivent être signées avec un certificat de développeur. Une application Android peut être générée dans deux modes : Debug (débogage) et Release (publication). Il est généralement recommandé d'avoir des certificats différents pour ces deux modes.  Les certificats utilisés pour signer les applications Android en mode Debug sont fournies avec le SDK Android.  Celui-ci est généralement installé automatiquement par Android Studio. Au moment de publier votre application dans Google Play, vous devez la signer avec un autre certificat, généralement généré par vous. Pour plus d'informations, voir [Signing your Android applications](http://developer.android.com/tools/publishing/app-signing.html).

Un magasin de clés contenant un certificat pour les environnements de développement est stocké dans un fichier `~/.android/debug.keystore`. Le mot de passe du magasin de clés par défaut est `android`. Ce certificat est utilisé pour générer des applications en mode Debug.

1. Extrayez l'empreinte digitale de votre certificat signataire depuis votre environnement de développement client :

	```XML
	keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore -list -v
	```

	Vous pouvez utiliser la même syntaxe pour extraire le hachage de clé du certificat du mode Release. Remplacez l'alias et le chemin du magasin de clés dans la commande.

1. Dans la boîte de dialogue des données d'identification Google Console, recherchez la ligne qui commence par `SHA1` sous la rubrique relative aux empreintes digitales de certificat. Copiez la valeur d'empreinte digitale qui a été obtenue en exécutant la commande **keytool** dans la zone de texte.

###Nom du package

1. Dans la boîte de dialogue des données d'identification, entrez le nom du package de votre application Android. 

  Pour trouver ce nom, ouvrez le fichier `AndroidManifest.xml` dans Android Studio et recherchez : 
  	
  	`<manifest package="{your-package-name}">`

1. Quand vous avez terminé, cliquez sur **Créer**. Ceci finalise la création des données d'identification.

###ID client Google

Une fois les données d'identification créées, la page relative à ces données affiche votre ID client Google. Notez cette valeur. Vous devez l'enregistrer dans l'application {{site.data.keyword.Bluemix}}.


## Configuration de {{site.data.keyword.amashort}} pour l'authentification Google
{: #google-auth-android-config}

Maintenant que vous disposez d'un ID client Google pour Android, vous pouvez activer l'authentification Google dans le tableau de bord {{site.data.keyword.amashort}}.

1. Ouvrez votre appli dans le tableau de bord {{site.data.keyword.Bluemix_notm}}.

1. Cliquez sur la vignette {{site.data.keyword.amashort}}. Le tableau de bord {{site.data.keyword.amashort}} se charge.

1. Cliquez sur le bouton configurer********de google sur le panneau.

1. Dans la zone relative à l'ID application pour Android, spécifiez votre ID client pour Android et cliquez sur **Sauvegarder**.

## Configuration du SDK client de {{site.data.keyword.amashort}} pour Android
{: #google-auth-android-sdk}

1. Revenez à Android Studio.

1. Ouvrez le fichier `build.gradle` du module de votre appli.

	Votre projet Android peut contenir deux fichiers `build.gradle` : un pour le projet et un autre pour le module de l'application. Utilisez le module de l'application.

  Localisez la section des dépendances et ajoutez une nouvelle dépendance de compilation pour le SDK client :

	```Gradle
	dependencies {
		compile group: 'com.ibm.mobilefirstplatform.clientsdk.android',
        name:'googleauthentication',
        version: '2.+',
        ext: 'aar',
        transitive: true
    	// autres dépendances
	}
	```
	**Remarque :** vous pouvez retirer la dépendance sur le module `core` du groupe `com.ibm.mobilefirstplatform.clientsdk.android`, si elle figure dans votre fichier. Le module `googleauthentication` le télécharge automatiquement. Le module `googleauthentication` télécharge et installe le SDK Google+ dans votre projet Android.

1. Synchronisez votre projet avec Gradle en cliquant sur **Tools (Outils) > Android > Sync Project with Gradle Files (Synchroniser le projet avec les fichiers Gradle)**.

1. Ouvrez le fichier `AndroidManifest.xml` de votre projet Android.

1. Ajoutez le droit d'accès à Internet sous l'élément `<manifest>` :

	```XML
	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.GET_ACCOUNTS" />
	<uses-permission android:name="android.permission.USE_CREDENTIALS" />
	```

1. Pour pouvoir utiliser le logiciel SDK client de {{site.data.keyword.amashort}}, vous devez l'initialiser en passant les paramètres **context** et **region**.

	En général, vous pouvez placer le code d'initialisation dans la méthode `onCreate` de l'activité
principale dans votre application Android, bien que cet emplacement ne soit pas obligatoire.

1. Initialisez le SDK client et enregistrez le gestionnaire d'authentification Google.

	```Java
	BMSClient.getInstance().initialize(getApplicationContext(), BMSClient.REGION_UK);

	BMSClient.getInstance().setAuthorizationManager(
					MCAAuthorizationManager.createInstance(this, "<MCAServiceTenantId>"));
						
	GoogleAuthenticationManager.getInstance().register(this);
	```

  * Remplacez `BMSClient.REGION_UK` par la région appropriée  Pour afficher votre région {{site.data.keyword.Bluemix_notm}}, cliquez sur l'icône **Avatar**  ![icône Avatar](images/face.jpg "icône Avatar")  dans la barre de menu pour ouvrir le widget **Compte et support**.  La valeur de région doit être l'une des suivantes : `BMSClient.REGION_US_SOUTH`, `BMSClient.REGION_SYDNEY` ou `BMSClient.REGION_UK`.
  * Remplacez `<MCAServiceTenantId>` par la valeur `tenantId` (voir [Avant de commencer](##before-you-begin)). 

   **Remarque :** si votre application Android a pour cible Android version 6.0 (API niveau 23) ou supérieure, vous devez vous assurer que l'application dispose d'un appel `android.permission.GET_ACCOUNTS` avant d'appeler `register`. Pour plus d'informations, voir [https://developer.android.com/training/permissions/requesting.html](https://developer.android.com/training/permissions/requesting.html){: new_window}.

1. Ajoutez le code suivant à votre activité :

	```Java
	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		GoogleAuthenticationManager.getInstance()
			.onActivityResultCalled(requestCode, resultCode, data);
	}
	```

## Test de l'authentification
{: #google-auth-android-test}
Une fois que le SDK client est initialisé et que le gestionnaire d'authentification Google est enregistré, vous pouvez commencer à envoyer des demandes à votre application de back end mobile.


Avant de commencer à tester, vous devez disposer d'une application back end mobile créée depuis le conteneur boilerplate **MobileFirst Services Starter**, ainsi que d'une ressource protégée par le noeud final {{site.data.keyword.amashort}} `/protected`. Pour plus d'informations, voir [Protection des ressources](https://console.{DomainName}/docs/services/mobileaccess/protecting-resources.html).

1. Essayez d'envoyer une demande à un noeud final protégé de votre application back end mobile dans votre navigateur de bureau en ouvrant `{applicationRoute}/protected`, par exemple : `http://my-mobile-backend.mybluemix.net/protected`.  Pour plus d'informations sur l'obtention de la valeur `{applicationRoute}`, voir [Avant de commencer](#before-you-begin). 

	Le noeud final `/protected` d'une application de back end mobile créée avec le conteneur boilerplate MobileFirst Services est protégé par {{site.data.keyword.amashort}}. Il n'est donc accessible qu'aux applications mobiles instrumentées avec le SDK client de {{site.data.keyword.amashort}}. Pour cette raison, le message `Unauthorized` s'affiche dans votre navigateur de bureau.

1. Utilisez votre application Android pour envoyer une demande au même noeud final protégé.Ajoutez le code ci-dessous après avoir initialisé l'instance `BMSClient` et enregistré `GoogleAuthenticationManager`.

	```Java
	Request request = new Request("{applicationRoute}/protected", Request.GET);
	request.send(this, new ResponseListener() {
		@Override
		public void onSuccess (Response response) {
			Log.d("Myapp", "onSuccess :: " + response.getResponseText());
			Log.d("MyApp",  MCAAuthorizationManager.getInstance().getUserIdentity().toString());
		}
		@Override
		public void onFailure (Response response, Throwable t, JSONObject extendedInfo) {
			if (null != t) {
				Log.d("Myapp", "onFailure :: " + t.getMessage());
			} else if (null != extendedInfo) {
				Log.d("Myapp", "onFailure :: " + extendedInfo.toString());
			} else {
				Log.d("Myapp", "onFailure :: " + response.getResponseText());
			}
		}
	});
	```

1. Lancez votre application. Un écran de connexion Google s'affiche. Après la connexion, l'application demande à être autorisée à accéder aux ressources :

	![image](images/android-google-login.png)

	L'interface utilisateur peut varier en fonction de votre périphérique Android et selon que vous êtes, ou non, connecté actuellement à Google.

  Cliquez sur **OK** pour autoriser {{site.data.keyword.amashort}} à utiliser votre ID utilisateur Google pour l'authentification.

1. 	Lorsque votre demande aboutit, la sortie suivante figure dans l'outil LogCat :

	![image](images/android-google-login-success.png)

 Vous pouvez également ajouter une fonctionnalité de déconnexion en ajoutant le code suivant :

 ```Java
 GoogleAuthenticationManager.getInstance().logout(getApplicationContext(), listener);
 ```

 Si vous appelez ce code lorsqu'un utilisateur est connecté via Google, l'utilisateur est déconnecté de Google. Lorsque l'utilisateur tente à nouveau de se
connecter, il doit alors sélectionner le compte Google sous lequel se reconnecter. S'il tente de se reconnecter avec un ID Google qu'il a déjà utilisé, ses données
d'identification ne lui sont pas redemandées. Pour que des données d'identification de connexion lui soient réclamées à nouveau, l'utilisateur soit supprimer
son compte Google du périphérique Android.

 La valeur `listener` transmise à la fonction de déconnexion peut être `null`.
