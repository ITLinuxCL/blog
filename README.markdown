## Manual
El blog de IT Linux usa [Octopress](http://octopress.org/) como motor de publicación, así que lo primero que recomendamos es que aprendas a [usar Octopress](http://octopress.org/docs/)

### Instalar Ruby
Lo primero que debes hacer es instalar una versión reciente de Ruby, se recomienda una versión igual o superior a Ruby 2.0.

Para los que usan Mac OS X Mavericks ya tienen instalado Ruby 2.0. Si usas otro S.O. y tienes una versión anterior de Ruby la recomendación es instalar una versión mayor con [rbenv](https://github.com/sstephenson/rbenv) o con [RVM](https://rvm.io).


### Fork el repositorio
Una vez que tengas instalado Ruby tienes que hacer un _fork_ del repo en Github con tu cuenta. Aquí hay un manual de como se hace [https://help.github.com/articles/fork-a-repo](https://help.github.com/articles/fork-a-repo)

### Clonar el repositorio
Una vez que tengas listo el Fork debes traertelo a tu equipo con el comando ```git clone```. Por ejemplo:

```bash
git clone git@github.com:ITLinuxCL/blog.git
```

La ayuda para sabe como se hace esto está en [https://help.github.com/articles/fork-a-repo](https://help.github.com/articles/fork-a-repo)


## Comienza a contribuir

### Crea tu post
Ahora ya tienes todo listo para comenzar a contribuir con tus artículos. Sigue esta guía para saber como escribir un nuevo post: [http://octopress.org/docs/blogging/](http://octopress.org/docs/blogging/).

### Actualiza tu repo
Guarda los cambios en tu repo de Github:

```bash
git add .
git commit "El titulo de mi articulo"
git push

### Genera un _pull request_
Una vez que hayas subido tus cambios a tu repo, debes generar un Pull Request para actualizar el repositorio oficial.

Como hacer un Pull Request: [https://help.github.com/articles/using-pull-requests](https://help.github.com/articles/using-pull-requests)

Una vez que hayas hecho el Pull Request el administrador del repo oficial (pbruna), recibirá una notificación y publicará tu nuevo artículo.
