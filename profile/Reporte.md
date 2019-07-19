PROFILING EN:

1-loop-interch.f90

**TIME

primero pruebo con time compilando cada vez con distintos niveles de optimización:

sin optimizar:

>>gfortran -O0 1-loop-interch.f90 -o 1-loop-interch-O0.e
>>time ./1-loop-interch-O0.e

output: (única vez que lo escribo porque siempre es el mismo output)
 good start
 good end
 bad start
 bad end

real	0m5,219s
user	0m4,885s
sys	0m0,324s

optimización de primer nivel 
>>gfortran -O1 1-loop-interch.f90 -o 1-loop-interch-O1.e
>>time ./1-loop-interch-O1.e

real	0m3,557s
user	0m3,216s
sys	0m0,300s

optimización de segundo nivel:
>>gfortran -O2 1-loop-interch.f90 -o 1-loop-interch-O2.e
>>time ./1-loop-interch-O2.e

real	0m3,271s
user	0m2,899s
sys	0m0,335s

optimización de tercer nivel:
>>gfortran -O3 1-loop-interch.f90 -o 1-loop-interch-O3.e
>>time ./1-loop-interch-O3.e

real	0m1,561s
user	0m1,204s
sys	0m0,318s

SE PUEDE VER QUE CADA NIVEL DE OPTIMIZACIÓN DISMINUYE EL TIEMPO DE EJECUCIÓN

**GPROF

compilando con distintos niveles de optimización
>>gfortran -O0 codigo.f90 -pg -o codigo.e
>>./codigo.e

se genera gmon.out y accedo con:
>>gprof programa.e gmon.out

Each sample counts as 0.01 seconds.
  %   cumulative   self              self     total           
 time   seconds   seconds    calls   s/call   s/call  name    
100.11      5.14     5.14        1     5.14     5.14  MAIN__

Comparando con la función time para el mismo nivel de optimización se observa que el tiempo cumulativo es ligeramente menor que el real time.

Cuando prendemos optimizaciones, el profiling puede ser mentiroso

**PERF

Performance counter stats for './1-loop-interch-O0.e':

       5437,230709      task-clock (msec)         #    0,964 CPUs utilized          
               818      context-switches          #    0,150 K/sec                  
                 7      cpu-migrations            #    0,001 K/sec                  
            73.339      page-faults               #    0,013 M/sec                  
     7.018.101.859      cycles                    #    1,291 GHz                      (49,91%)
     6.645.586.968      instructions              #    0,95  insn per cycle           (74,86%)
       541.817.771      branches                  #   99,650 M/sec                    (75,12%)
           336.136      branch-misses             #    0,06% of all branches          (74,98%)

       5,639569889 seconds time elapsed

El tiempo medido por PERF para el mismo ejecutable con el mismo nivel de optimización que antes es mayor que con GPROF y que con TIME.

 




