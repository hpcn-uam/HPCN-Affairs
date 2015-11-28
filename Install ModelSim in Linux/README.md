# Como instalar Modelsim en Linux

La última versión disponible para linux es la 10.1b_linux que utiliza las librerias de freetype-2.4.7 a demas no trae compiladas algunas librerias para simular, por lo tanto hay que instalarlas.

## Instalando ModelSim 
* Descargar la version de ModelSim 10.1b_linux
````
$ cd (directorio_descarga_modelsim)
$ ./install.linux
````
* Descargamos la libreria de freetype-2.4.7
````
$ wget download.savannah.gnu.org/releases/freetype/freetype-2.4.7.tar.gz
$ tar -zxvf freetype-2.4.7.tar.gz
$ cd freetype-2.4.7
$ ./configure
$ make -j2
````
* Copiar las librerias al directorio donde se instalo ModelSim
````
$ mkdir (directorio_instalacion_modelsim)/lib32
$ cp objs/.libs/* (directorio_instalacion_modelsim)/lib32
````
Ya tenemos las librerias compiladas y copiadas, ahora debemos modificar el ejecutable para que linke con esteas librerias

* Buscamos la linea dentro del archivo (directorio_instalacion_modelsim)/bin/vsim

`dir=\`dirname $arg0\``

A continuación agregamos la siguiente línea

`export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${dir}/lib32`

* Agregamos en el ~/.bashsrc o ~/.bash_profile para que el ejecutable se pueda lanzar desde la consola 
````
export PATH=$PATH:(directorio_instalacion_modelsim)/bin
export MODEL_TECH="(directorio_instalacion_modelsim)/linux_x86_64"
````
Hacemos que la variable de entorno MGLS_LICENSE_FILE apunte al servidor de licencias 

`export MGLS_LICENSE_FILE=(puerto)@servidor_de_licencias`

A partir de este momento ModelSim ya puede carga, pero es probable que si queremos simular algo diga que faltan librerias. Para solucionar esto debemos compilarlas desde Vivado.

## Compilar librerias para la simulación

* Abrimos Vivado

* Definimos donde esta el ejecutable de ModelSim 

para ello Tools -> Options -> General ModelSim install Path

![ModelSim_EXE](https://github.com/hpcn-uam/HPCN-Affairs/blob/master/Install%20ModelSim%20in%20Linux/vivado_general_options.png)

* Compilar las librerias 

Tools -> Compile Simulationr Libraries -> Compiled Library location : (directorio_instalacion_modelsim) 

![ModelSim_EXE](https://github.com/hpcn-uam/HPCN-Affairs/blob/master/Install%20ModelSim%20in%20Linux/vivado_library_options.png)

O ejecutando el siguente en la consola tcl de Vivado

````
compile_simlib -force -language all -dir {(directorio_instalacion_modelsim)} -simulator_exec_path {(directorio_instalacion_modelsim)/bin} -verbose  -library all -family  all
````

* Luego que se compilen las librerias hay que indicarle a ModelSim donde se encuentran

Modificar el archivo (directorio_instalacion_modelsim)/modelsim.ini

Agregamos allí las siguientes líneas
````
secureip 		= $MODEL_TECH/../secureip
simprims_ver 	= $MODEL_TECH/../simprims_ver
unifast  		= $MODEL_TECH/../unifast  	
unifast_ver  	= $MODEL_TECH/../unifast_ver 
unimacro  		= $MODEL_TECH/../unimacro  		
unimacro_ver  	= $MODEL_TECH/../unimacro_ver
unisim  		= $MODEL_TECH/../unisim  		 
unisims_ver		= $MODEL_TECH/../unisims_ver	
````
Por fin ya esta listo ModelSim para simular
