# Manifiestos de la plataforma

Este repositorio es la unica fuente de verdad de lo que corre en el namespace sa-p8 del cluster. ArgoCD lo observa y reconcilia el cluster contra su contenido. Nadie aplica manifiestos a mano ni desde un pipeline.

## Como esta organizado

Hay dos ambientes, produccion en el namespace sa-p8 y desarrollo en sa-p8-dev, y cada carpeta se divide en prod y dev.

Una Application raiz, creada por Terraform desde el repositorio de codigo, apunta a la carpeta apps, donde hay una Application por componente y por ambiente. Cada una toma su chart de Helm desde el repositorio de codigo, el archivo de valores del ambiente que corresponde dentro del chart, y encima los valores de despliegue de la carpeta values de este repositorio.

La Application politicas aplica al cluster las politicas de admision de Kyverno que viven en el repositorio de codigo, y como son de alcance de cluster cubre los dos ambientes a la vez.

La carpeta values contiene un archivo por componente y ambiente con la imagen y la etiqueta que debe correr. Son los archivos que el pipeline modifica mediante un pull request. Un push a la rama principal abre uno contra desarrollo con una version de prelanzamiento, y una etiqueta de version abre uno contra produccion. La carpeta secretos contiene las credenciales de cada ambiente cifradas con Sealed Secrets, que solo el controlador del cluster puede descifrar, junto con el certificado publico con el que se cifran.

## Como llega un cambio al cluster

El pipeline del repositorio de codigo construye, prueba, escanea y firma las imagenes, y despues abre un pull request aqui actualizando la etiqueta en la carpeta values. Al aprobarse y fusionarse, ArgoCD detecta la diferencia y sincroniza. Argo Rollouts toma la nueva version y la libera de forma progresiva, validando cada paso con un analisis automatico y revirtiendo si falla.

## Repositorio de codigo

https://github.com/jaguzaro/Practicas-SA-B-202201395
