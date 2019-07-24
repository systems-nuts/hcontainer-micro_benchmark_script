# Micro Benchmark Tester

## CONTENT SPECIFICATION

1. caller 

> The migration script, it has 4 args and migration a benchmark 3 times

> > - 1:test benchmark binary name

> > - 2:target machine, user@ip

> > - 3:the sender script, depends on criu branch, there are two sender for both archtectures

> > - 4:alternative args, some benchmark need args, for example _popcorn-mm 4096_

2. x86-64/

> The x86 sender and receiver scripts for migration

> > - arm_x86_receiver: recevier for x86 to handle migration from arm to x86

> > - x86_arm_sender: sender for x86 to handle migration from x86 to arm **CRIU branch: heterogeneous**

> > - x86_arm_sender_antonio: sender for x86 to handle migration from x86 to arm **CRIU branch: crit-in-criu**

> > - Makefile: install script to /user/local/bin/

3. aarch64/

> The aarch sender and receiver scripts for migration

> > - x86_arm_receiver: recevier for arm to handle migration from x86 to arm

> > - arm_x86_sender: sender for arm to handle migration from arm to x86 **CRIU branch: heterogeneous**

> > - arm_x86_sender_antonio: sender for arm to handle migration from arm to x86 **CRIU branch: crit-in-criu**

> > - Makefile: install script to /user/local/bin/

4. TEST/

> The popcorn-npb-is_c binaries for test.


## Pre-requisites

1. CRIU branch crit-in-criu

2. CRIU branch heterogenous

3. Login as root

4. Ubuntu 16.04

5. Linux 4.15.0-45-generic

6. Inorder to use script, target machines should have ssh public key to avoid password.

### SET UP SSH KEY
- sudo su

- ssh-keygen

- paste ~/.ssh/id\_dsa.pub to your target machine ~/.ssh/authorized\_keys
 
## ENVIORMENT SET UP FOR SCRIPT

1. Create same work directory, path: /popcorn/brdw. CMD: mkdir /popcorn/brdw

2. cp benchmark/\* /popcorn/brdw

3. cp caller /popcorn/brdw

## SCRIPT INSTALL

- On aarch64 
cd aarch64 ; make

- On x86-64
cd x86-64 ; make

## TEST

1. cd /popcorn/brdw/

2. ./caller $sender_script $benchmark $targetmachine $args
> there are two $sender_script, depends on criu branch, benchmark is test binary, $targetmachine is tagetmachine user@ip or alias, $args is for some binary _for example: ./popcorn-mm 4096_ 


## EXAMPLE: 

### Example for x86 to ARM, benchmark: popcorn-npb-is_c, CRIU: heterogeneous

- mkdir -p /popcorn/brdw/

- cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

- cp caller /popcorn/brdw/

- cd x86-64

- make

- ssh to aarch64 machine

- mkdir -p /popcorn/brdw/

- cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

- cp caller /popcorn/brdw/

- cd x86-64

- make

- cd popcorn/brdw/

- ./caller x86_arm_sender popcorn-linpack sunsky 

#### Example calling if benchmark needs fourth args (_popcorn-mm_)
 
- ./caller x86_arm_sender popcorn-mm sunsky 4096  

---

### Example for ARM to x86, benchmark: popcorn-npb-is_c, CRIU: crit-in-criu

- mkdir -p /popcorn/brdw/

- cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

- cp caller /popcorn/brdw/

- cd x86-64

- make

- ssh to aarch64 machine

- mkdir -p /popcorn/brdw/

- cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

- cp caller /popcorn/brdw/

- cd x86-64

- make

- cd popcorn/brdw/ 

- ./caller x86_arm_sender_antonio popcorn-linpack sunsky  

#### Example calling if benchmark needs fourth args (_popcorn-mm_)

- ./caller x86_arm_sender_antonio popcorn-mm sunsky 4096    



## **!!!SCRIPT BUG!!!**

1. Depends on machine, when to greb the process id, the script could run in some case

> modify the line 26 on sender script and line 13 on receiver script.

> > 	pid=$(ps -C $process | tr -s ' '  | cut -d ' ' -f 2 | tail -n +2)  
 
> > 	IF PID IS EMPTY, pleae change it between **"cut -d ' ' -f 2"** and  **"cut -d ' ' -f 1"** 

2. **(NEW UPDATE)** Latest popcorn-compiler will not have flash quit problem, so **while [ -z "$pid" ]** loop may delete( line 25 on sender and line 12 on receiver), the purpose of this loop is to make sure benchmark run successfully


~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                                                    
~                                                                      
