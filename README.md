@startuml
!theme plain
skinparam handwritten true
skinparam defaultTextAlignment center

' Style spécifique pour les diagrammes d'activité
skinparam activity {
    BackgroundColor #E8F8F5
    BorderColor Black
    DiamondBackgroundColor #FCF3CF
    DiamondBorderColor Black
}

title Logigramme TSG : Routage et Éclatement des Événements

start
:Nouveau mouvement comptable reçu
Topic : **cbs-booked** (STMT.ENTRY);

if (Le TRANSACTION.CODE est-il\ndans le catalogue des Frais ?) then (Non)
    #E8F8F5:Publier dans le topic
    **domain-transactions**;
    note right
      Opérations standards
      (Salaires, Achats CB, etc.)
    end note
    stop
else (Oui (C'est un frais))
    if (Est-ce un Frais Différé ?\n(Ex: Facture fin de mois)) then (Oui)
        :Recherche dans **billing-details**
        (via TRANS.REFERENCE);
        if (Détails trouvés ?) then (Oui)
            #B0D4F1:Éclater le mouvement global
            en **N** événements détaillés;
            
            #B0D4F1:Publier **N messages** dans
            **domain-fees**;
            note right
              1 événement par ligne de facture.
              Tous partagent le même "coreLedgerRef".
            end note
            stop
        else (Non)
            #FFCCCC:Envoyer en **DLQ** (Attente);
            note right: Désynchronisation Kafka
            stop
        endif
        
    else (Non)
        if (Est-ce un Frais d'Incident ?\n(Ex: Rejet soumis Murcef)) then (Oui)
            :Recherche dans **frmb-audit**
            (via TRANS.REFERENCE);
            if (Audit trouvé ?) then (Oui)
                #B0D4F1:Enrichir avec l'écrêtement
                (status, cappedAmount...);
                
                #B0D4F1:Publier 1 message enrichi dans
                **domain-fees**;
                stop
            else (Non)
                #FFCCCC:Envoyer en **DLQ** (Attente);
                stop
            endif
            
        else (Non (Frais simple immédiat))
            #B0D4F1:Mapping 1:1 direct
            (Pas besoin d'enrichissement);
            
            #B0D4F1:Publier 1 message dans
            **domain-fees**;
            note right
              Ex: Frais de virement
              immédiat
            end note
            stop
        endif
    endif
endif

@enduml


===

Le dictionnaire de codes : La fonction CATALOGUE_CODES_FRAIS est le cœur du réacteur. TSG doit charger en mémoire (idéalement via un Global KTable ou un cache Redis) la liste officielle des TRANSACTION.CODE que la banque considère comme des frais.

Le point d'ancrage (transReference) : Remarquez que les fonctions fetch_from_billing_topic et fetch_from_frmb_topic utilisent toujours la référence d'origine pour retrouver la donnée d'enrichissement. C'est votre clé de jointure.

Le filet de sécurité (DLQ) : Dans un système distribué asynchrone comme Kafka, le détail billing-details pourrait arriver quelques millisecondes après le mouvement cbs-booked. L'algorithme prévoit de mettre le message en attente (Dead Letter Queue ou State Store) pour le rejouer, plutôt que de crasher ou de générer un frais vide.
