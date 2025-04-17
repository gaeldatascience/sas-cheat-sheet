## **1. Procédures pour la gestion des données**
### **``PROC IMPORT``**
**Utilisée pour importer des fichiers externes (CSV, Excel, etc.) en tant que tables SAS.**
```sas
PROC IMPORT DATAFILE='chemin_du_fichier.csv' /* Chemin du fichier à importer */
    OUT=nom_table /* Nom de la table SAS créée */
    DBMS=CSV /* Type de fichier : CSV, XLSX, etc. */
    REPLACE; /* Remplace la table si elle existe déjà */
    /* GETNAMES=YES/NO; Indique si la première ligne contient les noms de colonnes. */
    /* DELIMITER=',' */
    /* GUESSINGROWS=MAX; */
RUN;
```
### **``PROC EXPORT``**
**Exporte des tables SAS vers des fichiers externes comme CSV ou Excel.**
```sas
PROC EXPORT DATA=nom_table /* Table SAS à exporter */
    OUTFILE='chemin_du_fichier.csv' /* Chemin et nom du fichier exporté */
    DBMS=CSV /* Type de fichier */
    REPLACE; /* Remplace le fichier s'il existe déjà */
RUN;
```
### **``ODS`` (Output Delivery System)**
**Exporte des tables SAS dans plusieurs formats (option `title`).**
```sas
ods rtf file="STAT_Report.rtf";
title "Rapport des Statistiques sur les Données STAT"; /* Ajouter un titre */
proc print data=STAT noobs;
run;
ods rtf close;
title; /* Réinitialise le titre */
/* rtf, pdf, excel, csvall */
### **``PROC SORT``**
**Trie une table SAS selon une ou plusieurs variables et permet d'éliminer les doublons.**
```sas
PROC SORT DATA=nom_table OUT=table_triee; /* Table triée en sortie */
    BY colonne1 colonne2; /* Trie par ordre croissant (par défaut) */
    /* BY DESCENDING colonne; : Trie une colonne en ordre décroissant. */
    /* OUT=table_triee NODUPKEY; : Supprime les doublons en fonction des colonnes listées. */
    /* NODUP : Tous les doublons indépendemment des colonnes listées. */
RUN;
```
## **2. Procédures pour les statistiques descriptives**
### **``PROC MEANS``**
**Fournit des statistiques descriptives comme la moyenne, la médiane, et les écart-types.**
```sas
PROC MEANS DATA=nom_table NOPRINT;
    VAR ma_variable;
    CLASS ma_variable2;
    OUTPUT OUT=stats_table(DROP=_TYPE_ _FREQ_) 
        N=
        NMISS=
        MEAN=
        MEDIAN=
        MIN=
        MAX=
        RANGE=
        VAR=
        STD=
        CSS=
        USS=
        CV=
        P1=
        P5=
        P10=
        P25=
        P50=
        P75=
        P90=
        P95=
        P99=
        / AUTONAME;
RUN;
PROC PRINT data=stats_table; run;
```
**Différences entre ``PROC MEANS`` et ``PROC UNIVARIATE`` :**
``PROC MEANS`` est rapide et centré sur les statistiques descriptives classiques comme la moyenne, la médiane, le minimum et l’écart-type, avec un support direct des regroupements via CLASS, mais ne propose ni tests de normalité, ni moments (asymétrie, kurtosis) ou graphiques.
``PROC UNIVARIATE`` est plus complet, offrant des tests de normalité, moments, percentiles personnalisés et graphiques (histogrammes, probplots), mais il est plus lent pour de grands volumes et nécessite BY avec tri préalable pour les regroupements.
### **``PROC UNIVARIATE``**
**Analyse la distribution des variables numériques (option avec graph), détecte les valeurs aberrantes et teste la normalité.**
**Version orientée statistiques :**
```sas
PROC UNIVARIATE DATA=nom_table NOPRINT;
    VAR ma_variable;
    OUTPUT OUT=stats_table
        N=N_obs
        NMISS=N_miss
        MEAN=Mean_value
        MEDIAN=Median_value
        MIN=Min_value
        MAX=Max_value
        RANGE=Range_value
        VARIANCE=Variance_value
        STD=StdDev_value
        SKEWNESS=Skewness_value
        KURTOSIS=Kurtosis_value
        PCTLPTS=10 25 50 75 90 
        PCTLPRE=Pctl_;
RUN;
```
**Version orientée graphique :**
```sas
PROC UNIVARIATE DATA=nom_table;
    VAR ma_variable;
    histogram ma_variable /* ou probplot *// normal;
	inset mean="Moyenne : " std="Ecart-type : " / position=ne; /* Pour rajouter des stats dans le graph */
    /* id id_variable; : Pour identifier les observations extrêmes */
run;
### **``PROC FREQ``**
**Analyse la distribution des variables catégoriques et produit des tableaux croisés.**
```sas
PROC FREQ DATA=nom_table;
    TABLES colonne / NOCUM MISSING LIST out=table_sortie; 
    /* TABLES colonne1 * colonne2 : Tableau croisé */
    /* NOCUM : Affiche les fréquences sans cumul */
    /* MISSING : Inclut les valeurs manquantes dans les calculs. */
    /* LIST : Affiche les fréquences sous forme de liste. */
    /* NOPERCENT NOROW NOCOL NOFREQ */
RUN;
```
## **3. Exploration des données**
### **``PROC PRINT``**
**Affiche les observations d'une table SAS.**
```sas
PROC PRINT DATA=nom_table (OBS=10); /* Limite à 10 observations */
   /* LABEL; : Affiche les étiquettes des colonnes au lieu des noms. */
   /* VAR colonne1 colonne2; : Sélectionne les colonnes à afficher. */
RUN;
```
### **``PROC CONTENTS``**
**Affiche des informations sur les tables, comme les noms de variables, types et formats.**
```sas
PROC CONTENTS DATA=nom_table;
   /* VARNUM; : Affiche les variables dans l'ordre où elles ont été créées. */
   /* SHORT; : Affiche un résumé compact des métadonnées. */
RUN;
```
## **4. Procédures pour l'analyse avancée**
### **``PROC REG``**
**Réalise des régressions linéaires simples et multiples.**
```sas
PROC REG DATA=nom_table;
    MODEL y = x1 x2 x3; /* Variable cible et prédicteurs */
    /* PLOT; : Affiche les résidus et diagnostics de régression. */
    /* SELECTION=STEPWISE; : Utilise une sélection pas à pas des variables. */
RUN;
```
QUIT;
### **``PROC CORR``**
**Calcule les coefficients de corrélation entre variables numériques.**
```sas
PROC CORR DATA=nom_table;
    VAR colonne1 colonne2; /* Variables à corréler */
    /* PEARSON; : Calcule les corrélations de Pearson. */
    /* SPEARMAN; : Calcule les corrélations de Spearman. */
RUN;
```
## **5. Rappel de la structure de la ``PROC SQL``**
```sas
PROC SQL outobs=N; /* Limite le nombre de lignes affichées dans la sortie */
/* VALIDATE : Vérifie la syntaxe sans exécuter */
CREATE TABLE nom_table AS
    SELECT /* colonnes à afficher */
    INTO :col1 SEPARATED BY ', ', :col2 SEPARATED BY ', '/* Pour créer des macros */
    /* Il faut autant de ":col" que de variables affichées par le SELECT. */
    /* "SEPARATED BY ', '" permet de stocker les valeurs en les séparant par ', ' */
    FROM /* table source */
    WHERE /* conditions pour filtrer les lignes */
    GROUP BY /* colonnes pour regrouper les données */
    HAVING /* conditions sur les groupes */
    ORDER BY /* colonnes pour trier les résultats (ASC par défaut, DESC)*/
;
QUIT;
```
## **6. Hechek ``Étape DATA``**
**Créer une table avec des données directement dans SAS**
```sas
DATA ventes;
    INPUT produit $ quantite prix_unitaire;
    /* le $ à la suite d'une variable indique que c'est une variable caractère */
    DATALINES;
Chaise 10 45.5
Table 5 120.99
Canapé 2 450.75
;
RUN;
```
**Manipulations de base**
```sas
DATA manipulations;
    SET table_entree;
    RENAME ancienne_variable = nouvelle_variable; /* Renommer une variable */
    KEEP produit nb_produits prix_unitaire categorie; /* Conserver uniquement ces variables */
    DROP prix_unitaire categorie; /* Supprimer ces variables */
    IF prix_unitaire > 10; /* Garder les lignes où la condition est remplie */
    IF _N_ <= 10; /* Conserve uniquement les 10 premières lignes */
    IF MISSING(quantite) THEN statut = "Manquant";
    ELSE statut = "Non manquant";
RUN;
```
**Conditions avec ``IF-THEN-ELSE``**
``IF-THEN-ELSE`` : Plus flexible, adapté aux conditions complexes.
```sas
DATA ventes_categorisees;
    SET ventes;
    IF prix_unitaire > 200 THEN categorie = "Haut de gamme";
    ELSE IF prix_unitaire > 100 THEN categorie = "Moyen de gamme";
    ELSE categorie = "Entrée de gamme";
RUN;
```
**Conditions avec ``SELECT-WHEN-OTHERWISE``**
``SELECT-WHEN-OTHERWISE`` : Plus lisible, idéal quand les conditions sont mutuellement exclusives.
```sas
DATA ventes_categorisees;
    SET ventes;
    SELECT;
        WHEN (prix_unitaire > 200) categorie = "Haut de gamme";
        WHEN (prix_unitaire > 100) categorie = "Moyen de gamme";
        OTHERWISE categorie = "Entrée de gamme";
    END;
RUN;
```
**Exemple pour l'exercice 7 de l'examen SAS Avancé 2023-2024**
```sas
data in.table_output_1 in.table_output_2;
    set in.table_programme_sql;
    select;
        when (substr(lowcase(var1), 1, 7) = 'libname' or substr(var1, 1, 2) = '/*') output in.table_output_1;
        when (var1 ne '') output in.table_output_2; /* Exclure les lignes vides ou invisibles */
        otherwise; /* Capture toutes les autres lignes */
    end;
run;
**Exemple des possibilités de ``RETAIN``, ``FIRST.`` et ``LAST.``**
```sas
DATA ventes_analyse;
    SET ventes;
    BY produit;

    /* Initialisation des variables avec RETAIN */
    RETAIN cumul_ventes max_vente indicateur_debut;

    /* Réinitialisation pour chaque nouveau groupe */
    IF FIRST.produit THEN DO;
        cumul_ventes = 0;
        max_vente = 0;
        indicateur_debut = 1;
    END;
    ELSE indicateur_debut = 0;

    /* Calculs pour chaque observation */
    cumul_ventes + quantite * prix_unitaire; /* Ajout au total cumulé */
    IF quantite * prix_unitaire > max_vente THEN max_vente = quantite * prix_unitaire; /* Mise à jour du maximum */

    /* Sortir uniquement la dernière observation */
    IF LAST.produit THEN OUTPUT;

    /* Conserver uniquement les variables nécessaires */
    KEEP produit quantite prix_unitaire cumul_ventes max_vente indicateur_debut;
RUN;
```
**Extraction de lignes spécifiques avec ``RETAIN`` et un *FLAG*.**
```sas
DATA lignes_extraites;
    SET log_data; /* Contient les lignes à analyser */
    RETAIN flag 0;

    /* Activer le flag si la ligne commence par 'proc sql' */
    IF SUBSTR(ligne, 1, 8) = 'proc sql' THEN flag = 1;

    /* Sortir les lignes où le flag est activé */
    IF flag = 1 THEN OUTPUT;

    /* Désactiver le flag si la ligne commence par 'quit' */
    IF SUBSTR(ligne, 1, 4) = 'quit' THEN flag = 0;

    /* Conserver uniquement la variable 'ligne' */
    KEEP ligne;
RUN;
```
**Jointures**
```sas
/* Jointures regroupées */
DATA table_sortie;
    MERGE table1(IN=a) table2(IN=b);
    BY id;

    /* Jointures */
    IF a AND b THEN OUTPUT inner_join; /* Inner Join (Présent dans les deux tables) */
    IF a THEN OUTPUT left_join;       /* Left Join (Toutes les lignes de table1) */
    IF b THEN OUTPUT right_join;      /* Right Join (Toutes les lignes de table2) */
    IF a AND NOT b THEN OUTPUT left_anti_join; /* Left Anti Join (Présent dans table1 mais pas dans table2) */
    IF b AND NOT a THEN OUTPUT right_anti_join; /* Right Anti Join (Présent dans table2 mais pas dans table1) */

    /* Full Join (toutes les observations des deux tables, aucune condition) */
    OUTPUT full_join;
RUN;
/* Concaténation verticale */
DATA concat_vertical;
    SET table1 table2; /* Empile les observations de table1 et table2 */
RUN;
```
## **7. Macros**
**Créer des Macro-Variables à partir de ``PROC SQL``**
```sas
PROC SQL;
    SELECT COUNT(*) INTO :nb_lignes /* Stocke le nombre d'observations dans la macro-variable nb_lignes */
    FROM table;
QUIT;
%PUT Le nombre de lignes est : &nb_lignes; /* Affiche la valeur de la macro-variable */
```
**Créer un chemin d'accès avec une Macro**
```sas
%LET chemin= C:\Users\MonUtilisateur\SAS\Data\;

DATA mydata;
    SET "&chemin.table.sas7bdat"; /* Utilisation du chemin défini */
RUN;
%PUT Le chemin défini est : &chemin; /* Affiche le chemin dans le log */
```
**Définir une Macro avec Paramètres**
```sas
%MACRO filtre_macro(dataset, variable, condition=);
    PROC PRINT DATA=&dataset;
        WHERE &variable &condition; /* Applique un filtre avec une condition dynamique */
    RUN;
%MEND;

/* Appeler la macro */
%filtre_macro(dataset=table, variable=age, condition=>30);
```

**Exemple de création de quartiles avec ``PROC MEANS``, stockage dans des Macro-Variables puis utilisation dans une heheck ``Etape DATA``.**
```sas
PROC MEANS DATA=dataset NOPRINT;
    VAR score; /* Variable pour laquelle on calcule les quartiles */
    OUTPUT OUT=quartiles PCTLPTS=25 50 75 PCTLPRE=Q_;
RUN;

DATA _NULL_;
    SET quartiles;
    CALL SYMPUTX('q1', Q_25); /* Quartile 1 */
    CALL SYMPUTX('median', Q_50); /* Médiane (Q2) */
    CALL SYMPUTX('q3', Q_75); /* Quartile 3 */
RUN;

DATA class_quartiles;
    SET dataset;

    /* Classification basée sur les quartiles */
    IF score < &q1 THEN categorie = 'Bas';
    ELSE IF score < &median THEN categorie = 'Moyen-Bas';
    ELSE IF score < &q3 THEN categorie = 'Moyen-Haut';
    ELSE categorie = 'Haut';
RUN;
```
**Macro qui boucle à partir d'une liste de macro-variables (examen 2023-2024)**
```sas
%macro suppression_librefs;

    data code_sql3;
        set in.code_sql2;
    run;

    /* Déterminer le nombre total de librefs dans librefs_list */
    %let nb_librefs = %sysfunc(countw(&librefs_list, %str( )));

    /* Boucle sur les librefs dans librefs_list */
    %do i = 1 %to &nb_librefs;

        /* Récupérer le libref courant */
        %let current_libref = %scan(&librefs_list, &i);

        /* Remplacer le libref suivi d'un point */
        data code_sql3;
            set code_sql3;
            new_code = tranwrd(new_code, "&current_libref..", " "); /* Remplace 'libref.' par un espace */
        run;

    %end;

%mend suppression_librefs;

%suppression_librefs;
```
## **8. FONCTIONS UTILES**
**Modification de variables**
```sas
/* SCAN : Extrait un mot spécifique d'une chaîne selon un délimiteur */
SCAN(string_variable, 2, '-');

/* SUBSTR : Extrait une sous-chaîne à partir d'une position donnée */
SUBSTR(string_variable, 3, 5); /* À partir du 3e caractère, longueur 5 */

/* Chercher si une chaîne est présente dans une variable */ 
INDEX(libelle, 'AIX') > 0;

/* UPCASE / LOWCASE : Convertit une chaîne en majuscules */
UPCASE(string_variable);
LOWCASE(string_variable);

/* COMPRESS : Supprime les espaces ou des caractères spécifiques d'une chaîne (ou TRIM()) */
COMPRESS(string_variable, ' '); /* Supprime les espaces */

/* CAT : Concatène des valeurs sans espace */
CAT(var1, var2);

/* CATX : Concatène des valeurs avec un séparateur (ici un espace) */
CATX(' ', var1, var2);

/* CATS : Concatène des valeurs en supprimant les espaces inutiles */
trimmed_concatenation = CATS(var1, var2);
```
**Calculs à partir de variables**
```sas
/* AVG : Calcule la moyenne des valeurs numériques */
AVG(var1, var2, var3);

/* MIN : Renvoie la valeur minimale */
MIN(var1, var2, var3);

/* INT : Extrait la partie entière d'un nombre */
INT(123.45);

/* ROUND : Arrondit un nombre à une précision donnée */
ROUND(var1, 0.01); /* Arrondit au centième */

/* Appeler une Macro dans une Etape DATA */
CALL SYMPUT('macro_nom', valeur);
/* Afficher une Macro */
%PUT Valeur de la macro : &macro_nom;
**Formats**
```sas
/* Extraire l'année / mois / jour / minute, etc */
YEAR(date_variable);
MONTH(date_variable);
DAY(date_variable);
MINUTE(date_variable);
TIME(date_variable);

/* MDY : Crée une date à partir de mois, jour et année */
MDY(12, 25, 2024);

/* INTNX : Calcule une nouvelle date en ajoutant des intervalles */
INTNX('MONTH', date_variable, 1); /* Ajoute 1 mois */

/* INTCK : Calcule la différence entre deux dates en intervalles */
INTCK('MONTH', date1, date2);
INTCK('minute', datetime_sas, 0, 'beginning') *Pour normaliser une date/heure à une granularité (ici minute);
INTCK('minute', '00:00:00't, timepart(datetime_sas))

/* Pour comparer des dates/heures */
'10FEB2020:00:00:00'dt ou '10FEB2020'd

/* A savoir sur les formats de date */
input(char_date, DDMMYY10.) *Si la chaîne de caractères suit le format DD/MM/YYYY;
DDMMYY10. = 10/02/2020
YYMMDD10. = 2020-02-10
DATE9. = 10FEB2020
TIME.

/* PUT = Numérique vers caractère */
put(num_var, 8.); /* percent8.2 transforme 0.8456 en 84.56% */

/* INPUT = Caractère vers numérique */
input(char_var, 8.);
```
### **``PROC FORMAT``**
**Mapping de valeurs (jobTitle) à des catégories (CSP) via un format personnalisé, puis ajoute ces catégories à une nouvelle colonne dans une table.**
```sas
/* Étape 1 : Préparer la table CNTLIN */
data format_table;
    set source_table; /* Table contenant les données de mappage */
    FMTNAME = 'custom_fmt'; /* Nom du format */
    TYPE = "C"; /* Spécifie que la variable est de type caractère */
    RENAME jobTitle = START; /* Valeur d'entrée */
    LABEL = CSP; /* Valeur de sortie */
run;

/* Étape 2 : Créer le format */
proc format cntlin=format_table;
run;

/* Étape 3 : Utiliser le format */
data transformed_table;
    set input_table; /* Table source */
    new_variable = put(jobTitle, custom_fmt.); /* Appliquer le format */
run;

/* Étape 4 : Vérifier le résultat */
proc print data=transformed_table;
run;
### **``PROC RANK``**
**Création et affectation de quantiles**
```sas
PROC RANK DATA=input_table OUT=output_table GROUPS=4;
    VAR score; /* Variable à classer */
    RANKS quartile_score; /* Nouvelle variable avec le quartile */
RUN;
```