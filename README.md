# Ejemplo con Helm
## Creacion de las imagenes

### Creacion de la imagen base

```
docker build -t devopsubuntu18.04:latest -f  agents/Dockerfile.baseimage  .
```

### ejecucion del pod inicial
creacion del agente con la capacidad init
```
docker build -t init-agent:latest \agents\init-agent\Dockerfile .
```

Usar docker tag para etiquetar la imagen
```
docker tag init-agent:latest jaysson1710/init-agent:latest
```
Usar docker push para cargarla al repositorio
```
docker push jaysson1710/init-agent:latest
```

### Agente de compilacion

creacion del agente de compilacion

```
docker build -t demand-agent:dotnet \agents\demand-agent\Dockerfile.dotnet .
```
Usar docker tag para etiquetar la imagen
```
docker tag demand-agent:dotnet jaysson1710/demand-agent:dotnet
```
Usar docker push para cargarla al repositorio
```
docker push jaysson1710/demand-agent:dotnet
```

La misma tarea para cada nueva image de compilacion a incluir.

## Configuracion de Helm repo e index

Crear el paquete (.tar) con package, para cada uno de los chart que vamos a generar y ubicarlos e la carpeta principal

Generar el index 
```
helm package .\charts\init-agent\
helm package .\charts\demand-agent\
helm repo index .
```

para hacer uso del paquete:
```
helm repo add azure https://raw.githubusercontent.com/jaysson1710/on-demand-agents/master
```

Al momento de desplegar el helm init (deployment)
se deben especificar los parametros:
* azdoUrl
* azdoPool
* azdoToken
* namespace

```
 helm install initconsola azure/azdo-init-agent -n project-foo --set env.azdoUrl=https://jaysson1710.visualstudio.com/ --set env.azdoToken=******** --set env.azdoPool=default -n project-foo --wait
 ```
 