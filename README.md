# ViewFans HTML5: Guide de démarrage
Ce guide a pour objectif de vous guider dans la mise en place du widget JS ViewFans dans votre site web.

Pour rappel, Viewpay est une solution de micro-paiement par l'attention publicitaire, qui permet à l'utilisateur de soutenir un éditeur ou créateur de contenu digital en regardant une publicité.

Voici un exemple du widget ViewFans en pied d'article : 

![sample](https://cdn.jokerly.com/images/logosVP/exemple_ViewPayFans.png?raw=true)

## Chargement du Javascript
```html
<script type="text/javascript" src="//cdn.jokerly.com/scripts/jkFbASQ.js"></script> 
```
Le fichier jkFbASQ.js est le seul fichier qui doit être chargé dans la page qui va accueillir Viewpay. Ce fichier est servi par notre CDN, pour vous garantir d'avoir toujours la dernière version.

NB: Il faut placer le script le plus haut possible dans la page afin d’optimiser son temps de chargement.

## Création du message de pied de page et bouton ViewPay
```html
<div id="btnShowViewPay">
<p id="VPthankyoutext">
<strong><mark>
Cet article vous a plu ? Pour nous permettre de continuer à financer la mise à disposition d’articles gratuits, vous pouvez regarder une courte publicité de votre choix en cliquant sur le bouton ci-dessous. Merci de votre soutien !
</mark></strong>
<button id="btnShowViewPay" onclick="VPloadAds()" style="background: url(&quot;https://cdn-stg.jokerly.com/siteDemo/img/minilogo.png&quot;) 10px center no-repeat rgb(27, 187, 236); padding-left: 30px; text-align: center; width: 50%; margin: 10px 25%;">Soutenez le site en visionnant une publicité</button>
</p></div>
```

## Création d’un div pour accueillir la publicité + fond noir 
Ajoutez un div avec l’id "cadreJokerlyADS" dans votre page + "VPmodal" pour faire ressortir la publicité. 
```html
<div id="VPmodal">
	<div id="cadreJokerlyADS"></div>
</div>
```

Voici le CSS de ces éléments : 

```css
#btnShowViewPay{
	display:none;
}
#cadreJokerlyADS{
	margin: auto;
	top: 0;
	right: 0;
	left: 0;
	position: fixed;
	bottom: 0;
	width: 650px !important;
	height: 450px !important;
	z-index:9999 !important;
}
//Le z-index est nécessaire à la visibilité de la publicité. 
//Il est impératif de le monter si nécessaire si une frame/bannière/autre est au dessus du nôtre.
//Ceci est obligatoire afin de garantir de CPM élevé.

@media screen and (max-width: 600px){
	#cadreJokerlyADS{
	width:100% !important;
	height:100% !important;
	margin-top:0;
	}
}
@media screen and ((min-width: 601px) and (max-width: 1024px)){
	#cadreJokerlyADS{
	width:100% !important;
	height:100% !important;
	margin-top:0;
	margin-left:0;
	}
}
#VPmodal{
    width: 100%;
    height: 100%;
    display: none;
    position: fixed;
    background-color: rgba(0, 0, 0, 0.9);
    z-index: 1000;
}
```
NB: pour toutes question concernant des adaptations du CSS, merci de prendre contact avec l'équipe ViewPay. 


La présence d'un fond sombre autour du système Viewpay permet d’optimiser l’expérience utilisateur. Voici un exemple du rendu:  
![sample](https://cdn.jokerly.com/images/logosVP/exemple_fondnoir.png)

Nous avons remarqué que les modales de Bootstrap sont par défaut affichées avec un délai de 0.15 secondes. Pour corriger ce problème dans un site Bootstrap, il suffit de lancer VPLoadAds dans un setTimeout d’au minimum 0.15s.

## Définition des fonctions et callbacks JavaScript

Les fonctions et callbacks fournis par Viewpay sont les suivants : 

Fonctions : 
VPinit() - appelée typiquement au onload d’une page pour vérifier la disponibilité d’une publicité. Cette fonction appelle le callback VPexistAds() si une ou plusieurs vidéos ont été trouvées, et appelle le callback VPnoAds() si aucune n’a été trouvée pour cet utilisateur.
VPloadAds() - appelée une fois que vous avez eu la confirmation qu’une pub existe (via le callback VPexistAds()). Permet d’ouvrir le système Viewpay.

Callbacks :
VPexistAds() - callback appelé si Viewpay a au moins une publicité à servir. Cette fonction vous permet d’afficher le bouton pour lancer Viewpay si vous recevez un OK. Si KO le callback VPnoAds() est appelé.
VPnoAds() - callback appelé si Viewpay n’a aucune publicité à servir.
VPplayAds() - callback de notification appelé lorsque l’utilisateur démarre la vidéo.
VPcompleteAds() - appelé quand l’utilisateur a fini le parcours.
VPcloseAds() - appelé si l’utilisateur ferme le parcours.

ViewPay est une solution qui répond aux appels reçus avec agilité et rapidité, il est donc avantageux pour vous de situer chacun des appels au début du chargement de la page Html.

Voici le code à intégrer dans une balise script : 
```javascript

<script> 
	function VPinitVideo(){
		JKFBASQ.init({
			site_id: 'b23d3f0235ae89e4', // id de démo fonctionnel - à changer impérativement avec votre contact ViewPay
			load_callback : VPexistAds,
			noads_callback : VPnoAds,
			complete_callback : VPcompleteAds,
			close_callback : VPcloseAds,
			play_callback : VPplayAds,
			cover:false,
		});
	}
		
	function VPexistAds(){
		console.log("existAds");
		document.getElementById("btnShowViewpay").style.display = "block";
	}
	function VPloadAds(){
    console.log("VPLoadAds");
    document.getElementById("VPmodal").style.display = 'block';
		JKFBASQ.loadAds();
	}
	function VPnoAds(){
		document.getElementById("btnShowViewpay").style.display = "none";
		console.log("VPNoAds");
	}
	function VPcompleteAds(){
		console.log("VPCompleteAds");
   	        document.getElementById("cadre").style.display = 'none';
		/*Une fois la pub finie, le code permettant de débloquer l’article doit se situer ici*/
	}
	function VPcloseAds(){
		console.log("closeAds");
   	        document.getElementById("modal").style.display = 'none';
	}
	function VPplayAds(){
    		console.log("VPPlayAds");
	}
</script>
 ```

Si vous voulez afficher un message de remerciement, insérez dans le VPcompleteAds() votre fonction/code correspondant.

Par exemple : 
```html
function VPcompleteAds(){ 
	var btn = document.getElementById("btnShowViewPay");
	btn.style.display="none";
	var thankyoutext = document.createElement("p");
	thankyoutext.style.textAlign="center";
	thankyoutext.style.fontWeight="bolder";
	thankyoutext.style.fontSize="x-large";	
	thankyoutext.innerHTML = "&#127881; La Tribune vous remercie pour votre soutien ! &#127881;";
	btn.parentNode.insertBefore(thankyoutext,btn.nextSibling);
}
```
![sample](https://cdn.jokerly.com/images/logosVP/exemple_ViewPayFansMerci.png?raw=true)

NB: La fonction VPinitVideo(), que vous avez configurée avec votre identifiant Viewpay (site_id), doit être appelée en amont de l’apparition du système Viewpay, afin de permettre à notre système d’aller interroger le catalogue de publicités pour trouver le contenu adapté. 

Exemple : 
```html
<body onload="VPinitVideo()">
...
</body>
```
Ou plus simplement dans une balise ```html <script>```

## Sécurisation du callback VPcompleteAds
L’ajout de cette fonctionnalité n’est pas obligatoire mais fortement conseillé afin de sécuriser l’utilisation du système Viewpay dans votre site.
Lorsqu’une transaction est validée par le serveur Viewpay, une requête HTTP va être effectuée par notre serveur vers une URL que vous nous aurez fournie, qui vous prévient qu’une transaction a été effectuée. Vous pouvez ainsi vous assurer que l’utilisateur a bien vu la publicité. 

Format de la requête
Lorsque le parcours Viewpay s’est déroulé avec succès, notre serveur appellera une URL de callback. (configurée dans votre compte Viewpay : voir avec votre contact chez Viewpay)

L’URL aura la forme suivante :
```html
http:///your.callback.url/callback_page.html?ref=&editor_id=&challenge=&shared_secret=
```
Votre URL de callback est l’URL qui est renseignée dans votre compte Viewpay.
Les paramètres ref, et challenge sont des paramètres spécifiques à des éditeurs anciens de Viewpay, et ne sont pas renseignés pour une nouvelle intégration. 
Le paramètre editor_id correspond au site_id qui vous a été communiqué avec la documentation d’intégration.
Le paramètre shared_secret est une clé cryptée définie dans votre compte Viewpay qui peut vous être envoyée afin de sécuriser la transaction (à voir avec votre contact chez Viewpay). (non obligatoire)
Vous pouvez éventuellement nous envoyer une clé spécifique à chaque utilisateur en renseignant dans l’initialisation de Viewpay un nouveau paramètre : secureId={yourSecureId}. Cette clé vous sera renvoyée avec le callback dans un paramètre avec un nom différent : dynamicParameter={yourSecureId}

Exemple : 
```javascript
// Dans l’init :
JKFBASQ.init({
	site_id: 'your_id',
	load_callback: VPexistAds,
	noads_callback: VPnoAds,
	complete_callback: VPcompleteAds,
	close_callback: VPcloseAds,
	play_callback : VPplay,
	cover:true,
	codeCategory:1,
	secureId : '012345aze',
	userInfo : {
		age:35,
		gender:'M',
		country:'FR',
		language:'fr',
		postcode:75018
	}
});

// Ce que vous recevrez dans le callback : 
http://your.callback.url/callback_page.html?...&editor_id={your_editorID}&dynamicParameter=012345aze
```
IMPORTANT : Pour valider la transaction, notre serveur attend une réponse HTTP valide avec le statut 200 (OK).
