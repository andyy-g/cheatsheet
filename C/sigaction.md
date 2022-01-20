tags: #IT #programming #C 
date: 14/01/22
***

bibliothèque C : <signal.h>

## Qu'est-ce qu'un signal ?
Un signal est un message émit à destination d'un processus (ou d'un groupe de processus) pour l'informer que quelque chose vient de survenir ou pour lui demander de réaliser une tâche particulière.

Les signaux ont des origines diverses, ils peuvent être :
- retransmis par le kernel : division par zéro, overflow, instruction interdite, ...,
- envoyés depuis le clavier par l’utilisateur (exemples : CTRL+Z, CTRL+C, CTRL+\, …),
- émis par la commande kill depuis le shell (kill -SIGNAL pid_process) ou par la fonction kill depuis un programme écrit en C (kill(pid_process, SIGNAL)).

Sous système Unix/Linux, vous pouvez obtenir la liste des signaux supportés par votre système d'exploitation avec la commande `kill -l`.

## signal() et sigaction()
Les fonctions signal() et sigaction() sont deux fonctions qui permettent de spécifier comment un signal doit être géré par un processus. 

Cependant la fonction signal() est maintenant dépréciée car non portable (son comportement varie selon les versions d'UNIX). Il faut lui préférer l'utilisation de la fonction sigaction(), qui est plus complexe mais offre plus de contrôle sur la gestion des signaux. 

## La structure sigaction
La structure de type `struct sigaction` est utilisée pour spécifier toutes les informations nécessaires pour la gestion d'un signal. Elle est définie dans le header signal.h comme ceci :

```
struct sigaction 
{
  void		(*sa_handler)(int); 
  sigset_t	sa_mask; 
  int		sa_flags;
};
```

**sa_handler **
Désigne la fonction à utiliser pour gérer le signal. Sa valeur peut être soit un pointeur sur fonction, soit l'une des deux constantes suivantes :
- SIG_IGN : indique que le signal doit être ignoré,
- SIG_DFL : indique que le signal doit être géré normalement.

NB : on ne peut pas appeler n'importe quelles fonctions dans une fonction handler, en particulier les fonctions non ré-entrante. La liste des fonctions async-signal-safe est disponible [ici](https://man7.org/linux/man-pages/man7/signal-safety.7.html).

**sa_mask**
Permet de définir un ensemsemble de signaux qui vont être bloqués pendant l'exécution de la fonction handler (spécifiée dans sa_handler). Généralement lorsqu'une fonction handler est appelée, on veut que son exécution se termine sans être interrompue par d'autres signaux qui pourraient perturber son exécution ou corrompre ses données.

NB1 : un signal intercepté par une fonction sigaction est automatiquement bloqué par défaut pendant l'exécution de la fonction handler, peu importe la valeur de sa_mask (ex : lorqu'un handler est mis en place pour le signal SIGTSTP, si d'autres signaux SIGTSTP sont reçus pendant l'exécution du handler, ces derniers sont en attente jusqu'à la fin de l'exécution).

NB2 : si la valeur de sa_handler est SIG_IGN ou SIG_DFL, le masque est ignoré. 

**sa_flags**
Permet de spécifier un ensemble d'attributs qui modifie le comportement du signal. 

L'un des attributs les plus utiles est SA_RESTART. Si un appel système ou l'appel d'une fonction de la bibliothèque C est interrompu par une fonction handler, le flag SA_RESTART permet de redémarré l'appel automatiquement après le retour de la fonction handler.

NB : le comportement de la fonction signal() est identique à celui de sigaction() avec l'attribut SA_RESTART.

## La fonction sigaction
prototype : `int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);`

**int signum**
Indique le signal auquel la fonction handler est associée (ex : SIGINT, SIGQUIT, ...).

/!\ signum ne peut pas prendre la valeur de SIGKILL et SIGSTOP car ces signaux ne peuvent pas être interceptés ou ignorés.

**const struct sigaction \*act**
Pointeur sur structure de type struct sigaction qui désigne la fonction handler pour le signal, un éventuel masque de signaux à bloquer, et les attributs à appliquer.

Si act est un pointeur null, l'action par défaut associé au signal est effectuée. 

**struct sigaction \*old_act**
Pointeur sur structure de type struct sigaction qui récupère les informations de l'action précédemment associée au signal, ce qui permet de vérifier quelle action était effectuée et éventuellement de la rétablir plus tard.

Si old_act est un pointeur null, les informations de l'ancienne action sont supprimées.

**Valeur de retour**
sigaction() renvoie 0 s'il réussit et -1 s'il échoue.

**Erreurs**
EFAULT : act ou oldact pointent en-dehors de l'espace d'adressage accessible.
EINVAL : un signal invalide est indiqué (ex : signal inexistant, SIGKILL, SIGSTOP).

## Exemple d'implémentation de sigaction()

```
#include <signal.h>
#include <stddef.h>
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <unistd.h>

typedef struct sigaction        t_sa;

void    handler_sigtstp(int signum)
{
        (void)signum;
        write(1, "Process can't be suspended\n", 27);
}

int     main(void)
{
        t_sa            sa_sigtstp;
        sigset_t        mask;
        int				x;

        sigemptyset(&mask);
        sigaddset(&mask, SIGINT);
        sigaddset(&mask, SIGQUIT);
        sa_sigtstp.sa_handler = handler_sigtstp;
        sa_sigtstp.sa_mask = mask;
        sa_sigtstp.sa_flags = SA_RESTART;
        if (sigaction(SIGTSTP, &sa_sigtstp, NULL) == -1)
		{
			perror("sigaction error");
			return (EXIT_FAILURE);
		}
        x = 0;
        while (1)
                x *= 0;
        return (0);
}
```
