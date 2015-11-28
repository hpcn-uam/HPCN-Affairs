Instalar Modelsim en Linux

La última versión disponible para linux es la 10.1b_linux que utiliza las librerias de freetype-2.4.7 a demas no trae compiladas algunas librerias para simular, por lo tanto hay que instalarlas.

Primero instalamos ModelSim 
````
$ ./install.linux
````
luego debemos descargar la libreria de freetype-2.4.7
````
$ wget download.savannah.gnu.org/releases/freetype/freetype-2.4.7.tar.gz
$ tar -zxvf freetype-2.4.7.tar.gz
$ cd freetype-2.4.7
$ ./configure
$ make -j2
````
Ahora debemos copiar las librerias al directorio donde se instalo ModelSim
````
$ mkdir (directorio_instalacion_modelsim)/lib32
$ cp objs/.libs/* (directorio_instalacion_modelsim)/lib32
````
Ya tenemos las librerias compiladas y copiadas, ahora debemos modificar el ejecutable para que linke con esteas librerias

buscamos la linea dentro del archivo (directorio_instalacion_modelsim)/bin/vsim

dir=`dirname $arg0`

A continuación agregamos el siguiente comando

export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${dir}/lib32

Ahora agregamos en el ~/.bashsrc o ~/.bash_profile lo siguiente 

export PATH=$PATH:(directorio_instalacion_modelsim)/bin
export MODEL_TECH="(directorio_instalacion_modelsim)/linux_x86_64"

Hacemos que la variable de entorno MGLS_LICENSE_FILE apunte al servidor de licencias 

export MGLS_LICENSE_FILE=(puerto)@servidor_de_licencias 

A partir de este momento ModelSim ya puede carga, pero es probable que si queremos simular algo diga que faltan librerias. Para solucionar esto debemos compilarlas desde Vivado.

Abrimos Vivado

Definimos donde esta el ejecutable de ModelSim, para ello Tools -> Options -> General ModelSim install Path

[ModelSim_EXE](/vivado_general_options.png)

Luego para compilar las librerias 

Tools -> Compile Simulationr Libraries -> Compiled Library location : (directorio_instalacion_modelsim) 

[ModelSim_EXE](/vivado_library_options.png)

O ejecutando el siguente en la consola tcl de Vivado

````
compile_simlib -force -language all -dir {(directorio_instalacion_modelsim)} -simulator_exec_path {(directorio_instalacion_modelsim)/bin} -verbose  -library all -family  all
````

Luego que se compilen las librerias hay que indicarle a ModelSim donde se encuentran esto se hace modificando el archivo (directorio_instalacion_modelsim)/modelsim.ini

Agregamos allí las siguientes líneas

secureip 		= $MODEL_TECH/../secureip
simprims_ver 	= $MODEL_TECH/../simprims_ver
unifast  		= $MODEL_TECH/../unifast  	
unifast_ver  	= $MODEL_TECH/../unifast_ver 
unimacro  		= $MODEL_TECH/../unimacro  		
unimacro_ver  	= $MODEL_TECH/../unimacro_ver
unisim  		= $MODEL_TECH/../unisim  		 
unisims_ver		= $MODEL_TECH/../unisims_ver	

Por fin ya esta listo ModelSim para simular
