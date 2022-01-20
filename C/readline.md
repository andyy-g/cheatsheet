tags: #IT #programming #C 
date: 18/01/22
***

bibliothèque C : <stdio.h>, <readline/readline.h>, <readline/history.h>

## A quoi sert la bibliothèque readline ?
GNU Readline est une bibliothèque de fonctions qui permet notamment d'afficher un prompt, de récupérer les ligne de commande tapées dans le terminal, de les éditer et de créer un historique de celles-ci. 

Readline utilise les raccourcis clavier d'Emacs par défaut, mais les raccourcis vi sont également disponibles.

## Comment utiliser la bibliothèque readline sur Linux ?
1. Vérifier si la bibliothèque readline est installée :
`ldconfig -p | grep libreadline`

2. Si ce n'est pas le cas, installer libreadline :
`sudo apt-get install libreadline-dev`

3. Compiler avec libreadline :
`gcc main.c -lreadline`

4. Si le linker n'arrive pas à trouver la bibliothèque (`undefined reference to readline`), chercher le path de la bibliothèque et des headers :
`find . -type f -name "libreadline*" 2>/dev/null`
`find . -type f -name "readline.h" 2>/dev/null`
`find . -type f -name "history.h" 2>/dev/null`

5. Renseigner les paths, par exemple  :
`gcc main.c -L/usr/lib -I/usr/include -lreadline`

## Quelques variables et fonctions de la bibliothèque readline
Liste non exhaustive des fonctions et variables disponible dans la libreadline.
 
 ### Variables
 En plus des fonctions, la bibliothèque readline met à disposition de nombreuses variables très utiles.
 
 **char \*rl_line_buffer**
 Contenu actuel de la ligne qui sera retournée par readline().
 
 **int rl_end**
 Nombre de caractères présent dans rl_line_buffer.
 
 **int rl_num_chars_to_read**
 Si initialisé à *n* avant l'appel à readline(), la fonction s'arrêtera et retournera rl_line_buffer après *n* caractères tapés.
 
 **char \*rl_prompt**
 Contenu du prompt utilisé par readline().
 
### Fonctions
 **readline**
 prototype : char \*readline(const char \*prompt);
 
 Affiche la string prompt dans le terminal, lit la ligne tapée dans le terminal et retourne cette dernière. 
 readline() offre par défaut les fonctionnalités d'édition similaire à celles d'un terminal (ex : ctrl-a/ctrl-e début/fin de ligne).
 
 Ex :  `input = readline("$> ");` affiche `$> ` dans le terminal et `input` contiendra la string tapée dans le terminal par l'utilisateur.
 
 NB1 : la string retournée par readline() est allouée avec malloc(), il faut donc la free() après utilisation.
 
 NB2 : readline() peut provoquer des leaks.

 **rl_on_new_line**
 prototype : int rl_on_new_line(void);
 
 Regénère le prompt. Celui-ci s'affiche uniquement lorsque le contenu de rl_line_buffer est modifé après l'appel à rl_on_new_line() (ex : le prompt n'est pas affiché tant qu'un caractère n'est pas tapé), c'est pourquoi cette fonction est souvent suivi de rl_redisplay().  
 
 **rl_replace_line**
 prototype : void rl_replace_line(const char \*new_text, int clear_undo);
 
 Remplace le contenu de rl_line_buffer avec new_text. Si clear_undo est non nulle, la undo list associée avec la ligne en cours est supprimée (ne pas s'en soucier si vous n'utilisez pas les fonctionnalités d'[undoing](https://tiswww.case.edu/php/chet/readline/readline.html#SEC34))
 
 **rl_redisplay**
 prototype : void rl_redisplay(void);
 
 Affiche le contenu actuel de rl_line_buffer.
 
 **add_history**
 prototype : void add_history(const char \*string);
 
 Ajoute string à la fin de l'historique.
 
 **rl_clear_history**
 prototype : void rl_clear_history(void);
 
 Supprime et free() tout les éléments présents dans l'historique.
 
 ## Exemple d'implémentation de readline
 
 ```
#include <stdio.h>
#include <readline/readline.h>
#include <readline/history.h>
#include <stdlib.h>
#include <string.h>

int	main(void)
{
	char	*input;

	while (1)
	{
		input = readline("> ");
		add_history(input);
		if (!strcmp(input, "exit"))
		{
			free(input);
			break ;
		}
		printf("size input = %d\n", rl_end);
		free(input);
	}
	rl_clear_history();
	return (0);
}
 ```