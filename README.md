# Mini-Projet DevOps : Pipeline CI/CD Sécurisée (E-commerce Java)

## Auteur
Fahmi Zarrougui

## Objectif
Mettre en place une chaîne DevOps complète pour le projet Java Maven :
- Compilation et tests automatisés
- Analyses SAST et DAST
- Déploiement automatique sur VM_Prod
- Monitoring (Netdata)
- Option : conteneurisation Docker

## Environnement
- VM_dev : Jenkins + Maven + Docker + SAST/DAST
- VM_Prod : Tomcat + Netdata

## Pipeline CI/CD
1. Checkout du code depuis GitHub
2. Build & tests Maven
3. Analyse SAST (Dependency-Check + SpotBugs)
4. Packaging (WAR)
5. Déploiement sur Tomcat (VM_Prod)
6. Scan DAST (OWASP ZAP)
7. Monitoring et reporting

## Livrables
- Jenkinsfile
- Rapports SAST et DAST
- Captures d’écran du pipeline et du dashboard
- Documentation PDF finale
