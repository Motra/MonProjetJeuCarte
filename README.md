
/* Fais moi  man si tu as besoin d'explication de la partie jouer une C4 sinon à lundi*/
/* Sinon autre chose, le jeu MD, j'ai mis la partie implementationsur drop box */


#include <stdio.h>

#include "C4.h"

char TexteCouleurC4[5][8] = {"", "Trefle", "Carreau", "Coeur", "Pique"};

/* Tableau de jeu */

 
typedef Tas SerieCouleurC4;
SerieCouleurC4 LigneC4[DerniereCouleur+1];
 Tas TalonC4;
	/* localisation des tas */
Localisation LocSeriesC4[DerniereCouleur+1];
Localisation LocTalonC4;

/* Formation du tableau de jeu initial */



void SaisirLocTasC4()
{
  int i;

  
  LocTalonC4.NC = 1;
  LocTalonC4.NL = 1;


  for (i=PremiereCouleur; i<=DerniereCouleur; i++)
    {
      LocSeriesC4[i].NC = 2*i+2;
      LocSeriesC4[i].NL = 3;
    }
}

void CreerTableauInitialC4()
{
Couleur Co;
int i;

  SaisirLocTasC4();
  

  /* Création du talon avec un jeu de 32 cartes  */
  CreerJeuNeuf(32, LocTalonC4, &TalonC4);
  BattreTas(&TalonC4);
   

  /* Création des séries de chaque couleur et initialisation avec le sept */

  for (Co=PremiereCouleur; Co<=DerniereCouleur; Co++)
    {
      CreerTasVide(LocSeriesC4[Co], etale, &(LigneC4 [Co]));

	for (i = 1; i<=8; i++){

      DeplacerHautSur(&TalonC4, &(LigneC4[Co]));
	}
      
    }
}




void ReformerTableauInitialC4()
{
  Couleur Co;
  int i, j;

  /* Le talon est reformé, en posant les tas des lignes sur Talon*/

  for (Co=PremiereCouleur; Co<=DerniereCouleur; Co++)
    {      
      PoserTasSurTas(&(LigneC4[Co]), &TalonC4);      
    }
  RetournerTas(&TalonC4);
  BattreTas(&TalonC4);

  /* On initialise les lignes des huit cartes */

  for (Co=PremiereCouleur; Co<=DerniereCouleur; Co++)
    {
      /*CreerTasVide(LocLigneC4[Co], etale, &(LigneC4[Co])); ???*/
      for(j=1;j<=8;j++)
      {
      DeplacerHautSur(&TalonC4, &(LigneC4[Co]));
      }
    }
}


/* Visualisation des états du jeu */

void AfficherC4()
{
  Couleur Co;

  EffacerGraphique();
  AfficherTas(TalonC4, "Talon");
  
  for (Co=PremiereCouleur; Co<=DerniereCouleur; Co++)
    AfficherTas(LigneC4[Co], TexteCouleurC4[Co]);
	
  AttendreCliquer();
}


void JouerUneC4(ModeTrace MT)
{
   
int i, Cpt;
Couleur Co, Color;
/*  jouer une carte C4*/

/*
-Prendre une carte dans l'un des tas de la serie 4
-la retourner
-la mettre en dessous du tas correspondant à sa couleur
*/
 if (MT == AvecTrace)
    AfficherC4();

Co = PremiereCouleur;

if (EstCachee(CarteSur(LigneC4[Co]))) {
RetournerCarteSur(&(LigneC4[Co]));	}

if (MT == AvecTrace)
    AfficherC4();

/*posons Color = la couler du tas (sous lequel ns devons la mettre) corespondant à la carte*/

Color = LaCouleur(CarteSur(LigneC4[Co]));

DeplacerHautSous(&(LigneC4[Co]), &(LigneC4[Color]));

if (MT == AvecTrace)
    AfficherC4();

/* Nous allons proceder le jeu comme ci: lorsqu'on prend la carte sur un tas T1 et on le met sous un Tas T2 correspondant à sa couleur---- et on prendra une autre carte sur ce dernier pour la mettre sous un tas correspondant à couleru et ainsi de suite ....*/

Co = Color;

while (EstCachee(CarteSur(LigneC4[Co]))){

	RetournerCarteSur(&(LigneC4[Co]));
	if (MT == AvecTrace)
    	AfficherC4();
	
	Color = LaCouleur(CarteSur(LigneC4[Co]));

	DeplacerHautSous(&(LigneC4[Co]), &(LigneC4[Color]));	
	if (MT == AvecTrace)
    	AfficherC4();

	Co = Color;

}



/*Une fois on sort dans la boucle tq, c'est qu'une carte découverte est atteinte dans un de 4 tas. Donc on doit proceder à regarder les autres cartes cachées (du sommet jusqu'à ce que la carte suivante soit decouverte) si leurs couleurs correspondent aux siennes*/



for (Co = PremiereCouleur; Co<=DerniereCouleur; Co++){
	while (EstCachee(CarteSur(LigneC4[Co])))
	{
		for (i=LaHauteur(LigneC4[Co]);i>=1;i--){
			if (LaCouleur(IemeCarte((LigneC4[Co]), i))!= Co)
      				{  Cpt=Cpt+1;} 
     
      
					
		}
	}
}

if (Cpt == 0) { printf("Bravo ! Vous avez gagné\n"); }
else  printf("Désolé ! Vous avez perdu\n"); 
}



void ObserverC4(int NP)
{
  int i;
  CreerTableauInitialC4();
  JouerUneC4(AvecTrace);

 for (i=1;i<=NP-1;i++)
   {
    ReformerTableauInitialC4();
    JouerUneC4(AvecTrace);
   }
}



/* La partie Analyser sans visualisation sur ecran !!!!!!*/

void AnalyserC4(int NP)
{
  int i, Win, PC, Cpt;
/* initialise le compteur de partie gangées*/
  Win = 0; 

/* Mise en plce dutableau puis une premierepartie jouée*/
  CreerTableauInitialC4();
  JouerUneC4(SansTrace);
  if (Cpt==0)
      Win=Win+1; 

/* on joue les np-1 parties restants tout en comptant le nbre de fois qu'on gagne ---pour evaluer par la suite les statistiques*/
  
 for (i=1;i<=NP-1;i++)
   {
    ReformerTableauInitialC4();
    JouerUneC4(SansTrace);
    if (Cpt==0)
      Win=Win+1;
   }
  
  PC= (Win*100)/NP; 
  printf("le pourcentage de gagne est de: %d \n",PC);
  
}
