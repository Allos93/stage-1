stage-1
=======
<?php 
session_start();

//Connexion a la base
ini_set("display_errors","off");


$idco=mysql_connect("localhost","root","plrtspa");
$idbase=mysql_select_db("testvva");
if(!$idco||!$idbase)
{
	echo "<script type=text/javascript>";
	echo "alert('Connexion impossible la base testvva')</script>";
}


// variables a retenir pour un meilleur traitement
$codeannee=$_GET['an'];

// Interrogations de la table ...
	// ... Année pour connaitre les codes années
	$reqdate= "SELECT * FROM Annee ";
	$resdate=mysql_query($reqdate,$idco);
	if($resdate)
	{
		while($infodate = mysql_fetch_row($resdate))
		{	$annee[ ]=array($infodate[0],$infodate[1],$infodate[2],$infodate[3]);}
	}
	// selection de toutes les années existantes
	
	//  ... Client
	$Clients="select * FROM Client";
	$rescl = mysql_query($Clients,$idco);
	if($rescl)
	{
		while($client=mysql_fetch_row($rescl))
		{
			$Clts[]=array($client[0],$client[1],$client[2],$client[3]);
		}
	}
	
	// ... Contact
	if(isset($_GET["cl"]))
	{
		$reqcont= "SELECT * FROM contact WHERE code_conv = ".$_GET["cl"];
		$rescont=mysql_query($reqcont,$idco);
		if($rescont)
		{
			while($Contact=mysql_fetch_row($rescont))
			{
				$Cont[]=array($Contact[1],$Contact[2],$Contact[3]."<BR />",$Contact[4],
				$Contact[5]."<BR />",$Contact[6],$Contact[7]."<BR />",$Contact[8],
				$Contact[9]."<BR />",$Contact[10]."<BR />");
			}
		}
	}
	
	// ... devis (pour savoir si le client selectionné a deja un devis)
	// si c'est le cas : retourne le numero de devis 
	// sinon : crée un devis avec l'année en cours
	$reqdevis= "SELECT numerodevis FROM Devis WHERE code_conv =".$_GET["cl"].
	" AND codeannee =".$codeannee;
	$resdevis=mysql_query($reqdevis,$idco);
	if($resdevis)
	{
		$lignumdevis=mysql_fetch_row($resdevis);
		$numdevis = $lignumdevis[0];
	}
	
	// ... période (pour les périodes existantes du client
	$reqperiode= "SELECT * FROM Periode WHERE numerodevis = ".$numdevis;
	$resperiode=mysql_query($reqperiode,$idco);
	if($resperiode)
	{
		while($ligneperiode=mysql_fetch_row($resperiode))
		{
			$numper[] = $ligneperiode[0];
			$Periode[]=array($ligneperiode[1],$ligneperiode[2],$ligneperiode[3],
			$ligneperiode[4],$ligneperiode[5],$ligneperiode[6],$ligneperiode[7]);
		}
	}

	// ... prestation avec nom du produit(prestations selon les devis)
	$reqprest="SELECT * from prestation where numerodevis=".$numdevis;
	$resprest=mysql_query($reqprest,$idco);
	if(resprest)
	{
		while($ligneprest=mysql_fetch_row($resprest))
		{
			$numprest[] = $ligneprest[0];
			$Prestation[] = array ($ligneprest[1],$ligneprest[2],$ligneprest[3],$ligneprest[4]);
		}
	}
	
	//... Produit (pour pouvoir la modifier dans le devis)
	$reqprod="SELECT * FROM Produits";
	$resprod = mysql_query($reqprod,$idco);
	if($resprod)
	{
		while($prod=mysql_fetch_row($resprod))
		{
			$Produits[]=array($prod[0],$prod[1],$prod[2],$prod[3]);
		}
	}
	
	// ... profil (pour connaitre les droits des utilisateurs)
	if($_SESSION['Util'])
	{
		$Droits="SELECT * FROM Profil WHERE idprofil = (SELECT profil FROM Utilisateurs
		WHERE pseudo='".$_SESSION['NomUtil']."')";
		$resdroit = mysql_query($Droits,$idco);
		if($resdroit)
		{
			$ledroit= mysql_fetch_row($resdroit);
			$_SESSION['Droit']=$ledroit[2];	
			
		}
	}
	
	// ... unite
	$prix="SELECT * FROM unite";
	$resprix=mysql_query($prix,$idco);
	if($resprix)
	{
		while($p=mysql_fetch_row($resprix))
		{
			$Priu[]=array($p[0],$p[1],$p[2]);
		}
	}

	
?>
