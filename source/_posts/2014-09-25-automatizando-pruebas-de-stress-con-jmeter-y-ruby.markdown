---
layout: post
title: "Automatizando pruebas de stress con JMeter y Ruby"
date: 2014-09-25 22:16
comments: true
categories: [web,ruby,bash]
keywords: linux,seguridad,bash
description: Un simple tutorial de como realizar pruebas sobre nuestras aplicaciones con JMeter
---
Una excelente forma de validar nuestras aplicaciones y sitios web consiste en someterlos a pruebas de carga para comprobar como se comportarán una vez que vean la luz de Internet.

[JMeter](http://jmeter.apache.org) es una de las mejores herramientas Open Source para realizar pruebas de carga. Además de HTTP soporta protocolos como POP3, IMAP, FTP, etc. y también permite programar lo que llama [Planes de Prueba](http://jmeter.apache.org/usermanual/build-test-plan.html) que consisten en guíones de ejecución para las pruebas de stress.

Con todo el poder y opciones que ofrece JMeter, también viene toda la dificultad de escribir los Planes de Prueba y también el hecho de que como desarrolladores o administradores preferimos la calidez de la consola que la frialdad de una GUI Java. Aquí es donde entra Ruby, y específicamente [ruby-jmeter](http://jmeter.apache.org).

ruby-jmeter es una gem que permite escribir planes de JMeter usando una DSL de Ruby y también nos deja ejecutar estos planes desde la línea de comandos sin necesidad de utilizar interfaz gráfica.

```ruby
require 'ruby-jmeter'

test do
  threads count: 60 do 
	  visit name: "google_search", url: "http://www.google.com/"
  end
end.run(
  path: '/opt/jmeter/bin/',
  file: 'jmeter.jmx',
  log: 'jmeter.log',
  jtl: 'results.jtl',
  properties: 'jmeter.properties'
  )
```

Del script anterior destacamos:

* **línea 4**: cuantos usuarios queremos simular
* **línea 5**: Un tag y la página a visitar
* **línea 8**: El path donde está el ejecutable de JMeter
* **línea 11**: El archivo con los resultados de las pruebas
* **línea 12**: La configuración de JMeter, ajustado para que guarde el resultado en CVS y use como delimitador ```,```

Un extracto del resultado del test, del archivo ```results.jtl```, sería como sigue:

```
1411639664180,348,google_search,200,OK,ThreadGroup 1-3,text,true,129298,343
1411639664525,327,google_search,200,OK,ThreadGroup 1-4,text,true,129298,320
1411639665071,266,google_search,200,OK,ThreadGroup 1-5,text,true,129298,262
1411639664335,353,google_search,200,OK,ThreadGroup 1-4,text,true,129298,320
1411639665071,266,google_search,200,OK,ThreadGroup 1-5,text,true,129298,262
```

El beneficio de poder programar las pruebas de stress con Ruby, o con otro lenguaje, es que estás se pueden automatizar y por ejemplo incluirlas en el proceso de liberación de las aplicaciones usando un sistema de integración continua.

Otro beneficio es que al estar automatizadas, las pruebas si se harán, porque reconozcamos que a veces es una lata.

En un próximo artículo revisaremos como extraer e interpretar la información del archivo ```results.jtl```, creando resúmenes y gráficas.
