Buenas practicas gitflow:

- Dockerfile y docker-compose dentro del repo de la aplicacion.

- Cuando estas programando, estas programando en tu rama feature, ticket etc..., y esa pasa a la rama dev.
- Luego se testearia y pasaria a la rama de testing o staging

La estructura estandard de las rama

- rama produccion o master --> release manual o automatica
- |
- rama testing o staging --> automatizar test unitarios, chequeos de seguiridad, suele ser identico a infraestructura y esquema de produccion, pero no en tamaño
- |
- rama dev --> integracion de todo el codigo
  - |
  - ramas features o tickets --> tu rama donde trabajas

Github, gitlab te permite hacer locks para que el codigo no pueda pasar a mano de la rama feature a main. hotfix es la excepcion y hay personas encargadas y autorizadas.

![Imagen](https://raw.githubusercontent.com/sosan/kubernetes/master/assets/gitflow.png)


