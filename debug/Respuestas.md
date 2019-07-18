BUGS:

Leyendo MkFile me dice como llamar cada programa por su nombre.
Primero compilo sin flags, es decir, dejo el MkFile como está.

------add_array_nobugs.c-----
>>make nobugs

>>gcc  -g -c add_array_nobugs.c -o add_array_nobugs_c.o
ME DEVUELVE UN WARNING:
add_array_nobugs.c: In function ‘add_array’:
add_array_nobugs.c:7:12: warning: implicit declaration of function ‘abs’ [-Wimplicit-function-declaration]
     sum += abs(a[i]);
            ^~~
gcc  add_array_nobugs_c.o -o add_array_nobugs.e

PERO PUEDO EJECUTAR TRANQUILAMENTE
>>./add_array_nobugs.e 
The addition is 6

AHORA CORRO EL DEBUGGER QUE NO ME TIRA NADA DEMASIADO INTERESANTE


AHORA CORRO MK FILE CON EL FLAG -g -O0
o sea que modifico: CFLAGS = -g -O0

-g es el flag de debuggeo
-O0 es el flag de no optimización por parte del compilador

PRIMERO EJECUTANDO 
>>make clean

RECOMPILANDO CON LOS NUEVOS FLAGS OBTENGO:

make nobugs
gcc  -g -O0 -c add_array_nobugs.c -o add_array_nobugs_c.o
add_array_nobugs.c: In function ‘add_array’:
add_array_nobugs.c:7:12: warning: implicit declaration of function ‘abs’ [-Wimplicit-function-declaration]
     sum += abs(a[i]);
            ^~~
gcc  add_array_nobugs_c.o -o add_array_nobugs.e

CORRIENDO EL DEBUGGER
>>gdb ./add_array_nobugs.e 
>>(gdb)run

ME TIRA QUE FUNCIONA PERFECTAMENTE:

Starting program: /home/lior/Documents/WORKSHOP_PYTHON/DIA4/HOdebug-profile/debug/bugs/add_array_nobugs.e 
The addition is 6
[Inferior 1 (process 3868) exited normally]

--------------add_array_segfault.c----------------
EJECUTO
>>make clean
>>make segfault

MANTENIEDO LAS BANDERAS DE DEBUGGEO Y NO OPTIMIZACIÓN OBTENGO UN WARNING
CORRIENDO AHORA
>>gdb ./add_array_segfault.e
Y LUEGO
>>run
obtengo:
Program received signal SIGSEGV, Segmentation fault.
0x000055555555470f in main (argc=1, argv=0x7fffffffdcf8)
    at add_array_segfault.c:19
19	    b[i] = i;

INDICANDO UN ERROR DE SEMENTACIÓN EN LA LINEA 19, O SEA QUE ESTOY USANDO ESPACIO EN MEMORIA AL QUE NO PUEDO ACCEDER.

Mirando el código hay un obvio problema entre la suma de matrices (dimensión 5)
y el tamaño de los arreglos (tamaño 3)
cambiando eso y repitiendo los pasos sigue apareciendo un error de segmentación 
EL problema resulta ser que los punteros a "a" y "b" fueron asignados de forma arbitraria y se "pisan" los valores, fijando los valores de los arreglos a y b o usando malloc se reselve el problema de segmentación.

Este último error se puede hallar haciendo un 
>>(gdb) print &a
>>(gdb) print &b
y viendo que estan mas cerca que a distancia de un word (byte), siendo imposible que haya lugar para ambos arreglos en forma consecutiva dentro de la memoria.

-----------add_array_static.c--------------------

-----------add_array_dynamic.c--------------------

EJECUTO
>>make clean
>>make segfault

MANTENIEDO LAS BANDERAS DE DEBUGGEO Y NO OPTIMIZACIÓN OBTENGO UN WARNING
CORRIENDO AHORA
>>gdb ./add_array_segfault.e
Y LUEGO
>>run
obtengo:
Starting program: /home/lior/Documents/WORKSHOP_PYTHON/DIA4/HOdebug-profile/debug/bugs/add_array_dynamic.e 
The addition is 6
[Inferior 1 (process 4384) exited normally]

POR LO ANTERIOR PARECE ANDAR TODO BIEN, POR SUGERENCIA DE GRACIELA cambiamos el comienzo del código definido para sumar matrices para que haga producto de matrices.
Ejecuto todo igual que antes pero la salida del run ahora arroja.
>>Starting program: /home/lior/Documents/WORKSHOP_PYTHON/DIA4/HOdebug-profile/debug/bugs/add_array_dynamic.e 
The addition is 0
[Inferior 1 (process 5064) exited normally]

DA CERO! UN PRODUCTO DE NUMEROS DISTINTOS DE CERO! PERO NO TIRA ERROR, el problema se debe a que definimos un arreglo desde el "sitio 1 a 4" pero usamos los arreglos de "sitio 0 a 3" con lo cual estamos dejando 2 lugares con cero que uso para multiplicar arrojando resultado cero.
Solución: recorrer de la misma manera los for.
Ejecutando todo nuevamente obtengo el resultado correcto: 36.





