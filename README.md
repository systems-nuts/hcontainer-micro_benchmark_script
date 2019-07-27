# Micro Benchmark Tester

## Contents

1. caller 
```
The migration script, it has 4 args and run migration of benchmark 3 times

- test benchmark binary name
- target machine, user@ip
- the sender script, depends on criu branch, there are two sender for both archtectures
- alternative args, some benchmark need args, for example _popcorn-mm 4096_
```
2. x86-64/
```
The x86 sender and receiver scripts for migration

- arm_x86_receiver: recevier for x86 to handle migration from arm to x86
- x86_arm_sender: sender for x86 to handle migration from x86 to arm **CRIU branch: heterogeneous**
- x86_arm_sender_antonio: sender for x86 to handle migration from x86 to arm **CRIU branch: crit-in-criu**
- Makefile: install script to /user/local/bin/
```
3. aarch64/
```
The aarch sender and receiver scripts for migration

- x86_arm_receiver: recevier for arm to handle migration from x86 to arm
- arm_x86_sender: sender for arm to handle migration from arm to x86 **CRIU branch: heterogeneous**
- arm_x86_sender_antonio: sender for arm to handle migration from arm to x86 **CRIU branch: crit-in-criu**
- Makefile: install script to /user/local/bin/
```
4. TEST/
```
The popcorn-npb-is_c binaries for test.
```

## Pre-requisites
1. Recommended system Linux Linux 4.15.0-45-generic
2. Recommended os Ubuntu 16.04
3. CRIU branch heterogenous-simplified. (recommended)
```
$ git clone https://github.com/systems-nuts/criu.git
$ git checkout hetterogeneous-simplified
branch crit-in-criu
$ git checkout crit-in-criu (alternative)
```
4. Inorder to use script, target machines should have ssh public key to avoid password.
```bash
sudo su
ssh-keygen
paste ~/.ssh/id\_dsa.pub to your target machine: ~/.ssh/authorized\_keys
```
5. root permission
7. Popcorn-compiler
```bash
$ git clone  https://github.com/systems-nuts/popcorn-compiler.git
$ git checkout criu
```

## Environment set up

Create same work directory on both machines, path: /popcorn/brdw. 
```bash
mkdir /popcorn/brdw
cp benchmark/\* /popcorn/brdw
cp caller /popcorn/brdw
```

## Script installation

- On aarch64 
```bash
cd aarch64 ; make
```
- On x86-64
```bash
cd x86-64 ; make
```

## Test
```bash
cd /popcorn/brdw/
./caller $sender_script $benchmark $targetmachine $args
```
there are two $sender_script, depends on criu branch

1. $benchmark is test binary, 

2. $targetmachine is tagetmachine user@ip or alias, 

3. $args is for some binary _for example: ./popcorn-mm 4096_ 

## Build: 

### Example for x86 to ARM, benchmark: popcorn-npb-is_c, CRIU: heterogeneous
```bash
mkdir -p /popcorn/brdw/

cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

cp caller /popcorn/brdw/

cd x86-64

make

ssh to aarch64 machine

mkdir -p /popcorn/brdw/

cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

cp caller /popcorn/brdw/

cd x86-64

make

cd popcorn/brdw/

./caller x86_arm_sender popcorn-linpack sunsky 
```

#### Example calling if benchmark needs fourth args (_popcorn-mm_)
```bash 
./caller x86_arm_sender popcorn-mm sunsky 4096  
```
### Example for ARM to x86, benchmark: popcorn-npb-is_c, CRIU: crit-in-criu
```bash
mkdir -p /popcorn/brdw/

cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

cp caller /popcorn/brdw/

cd x86-64

make

ssh to aarch64 machine

mkdir -p /popcorn/brdw/

cp ./TEST/popcorn-npb-is_c\* /popcorn/brdw/

cp caller /popcorn/brdw/

cd x86-64

make

cd popcorn/brdw/ 

./caller x86_arm_sender_antonio popcorn-linpack sunsky  
```
#### Example calling if benchmark needs fourth args (_popcorn-mm_)
```bash
./caller x86_arm_sender_antonio popcorn-mm sunsky 4096    
```
## **Known bug**

1. Depends on machine, when to greb the process id, the script could run in some case
```
modify the line 26 on sender script and line 13 on receiver script.
	
pid=$(ps -C $process | tr -s ' '  | cut -d ' ' -f 2 | tail -n +2)  
 	
 IF PID IS EMPTY, pleae change it between **"cut -d ' ' -f 2"** and  **"cut -d ' ' -f 1"** 
```
2. **(NEW UPDATE)** 
```
Latest popcorn-compiler will not have flash quit problem, 

while [ -z "$pid" ] loop may delete( line 25 on sender and line 12 on receiver)

the purpose of this loop is to make sure benchmark run successfully
```