# Manifiestos de la plataforma

Este repositorio es la unica fuente de verdad de lo que corre en el namespace sa-p8 del cluster. ArgoCD lo observa y reconcilia el cluster contra su contenido. Nadie aplica manifiestos a mano ni desde un pipeline.

## Como esta organizado

Una Application raiz, creada por Terraform desde el repositorio de codigo, apunta a la carpeta apps, donde hay una Application por componente de la plataforma. Cada una toma su chart de Helm desde el repositorio de codigo y sus valores de despliegue desde la carpeta values de este repositorio.

La carpeta values contiene un archivo por componente con la imagen y la etiqueta que debe correr. Son los archivos que el pipeline de integracion modifica mediante un pull request cuando publica una version nueva. La carpeta secretos contiene las credenciales de la plataforma cifradas con Sealed Secrets, que solo el controlador del cluster puede descifrar.

## Como llega un cambio al cluster

El pipeline del repositorio de codigo construye, prueba, escanea y firma las imagenes, y despues abre un pull request aqui actualizando la etiqueta en la carpeta values. Al aprobarse y fusionarse, ArgoCD detecta la diferencia y sincroniza. Argo Rollouts toma la nueva version y la libera de forma progresiva, validando cada paso con un analisis automatico y revirtiendo si falla.

## Repositorio de codigo

https://github.com/jaguzaro/Practicas-SA-B-202201395
