# DawnCC - A Source to Source Compiler 

## Introduction

### Motivation

In recent times, there has been an increasing interest in general-purpose computing on graphics processing units (GPGPU). This practice consists of developing general purpose programs, i.e., not necessarily related to graphics processing, to run on hardware that is specialized for graphics computing. Executing programs on such chips can be advantageous due to the parallel nature of their architecture: while a typical CPU is composed of a small number of cores capable of a wide variety of computations, GPUs usually contain hundreds of simpler processors, which perform operations in separate chunks of memory concurrently. Thus, a graphics chip can run programs that are sufficiently parallel much faster than a CPU. In some cases, this speedup can reach several orders of magnitude. Some experiments also show that not only can GPU execution be faster, but in many cases they are also more energy-efficient. 

This model, however, has its shortcomings. Historically, parallel programming has been a difficult paradigm to adopt, usually requiring that developers be familiarized with particular instruction sets of different graphics chips. Recently, a few standards such as OpenCL and CUDA have been designed to provide some level of abstraction to these platforms, which in turn has led to the development of several compiler directive-based models, e.g. OpenACC and offloading in OpenMP. While these have somewhat bridged the gap between programmers and parallel programming interfaces, they still rely on manual insertion of compiler directives in production code, an error-prone process that also commands in-depth knowledge of the target program. 

Amongst the hurdles involved in annotating code, two tasks are particularly challenging: identifying parallel loops and estimating memory bounds. Regarding the former, opportunities for parallelism are usually buried in complex code, and rely on non-trivially verifiable information, such as the absence of pointer aliasing. As to the latter, languages such as C and C++ do not provide any information on the size of memory being accessed during the execution of the program. However, when offloading code for parallel execution, it is necessary to inform which chunks of memory must be copied to other devices. Therefore, the onus of keeping track of these bounds falls on the programmer.
### Implementation

We have developed DawnCC as a tool to automate the performance of these tasks. Through the implementation of a static analysis that derives memory access bounds from source code, it infers the size of memory regions in C and C++ programs. With these bounds, it is capable of inserting data copy directives in the original source code. These directives provide a compatible compiler with information on which data must be moved between devices. Given the source code of a program as input, our tool can then provide the user with a modified version containing directives with proper memory bounds specified, all without any further intervention from the user, effectively freeing developers from the burdensome task of manual code modification. 

We implemented DawnCC as a collection of compiler modules, or passes, for the LLVM compiler infrastructure, with this webpage functioning as a front-end for users to provide program source codes as input. The program is then compiled by LLVM and transformed by our passes to produce the modified source code as output to the webpage user. There are also a few options to customize the output, such as printing runtime statistics.

## Functionality

### Compiler Directive Standards

Compiler directive-oriented programming standards are some of the newest developments in features for parallel programming. These standards aim to simplify the creation of parallel programs by providing an interface for programmers to indicate specific regions in source code to be run as parallel. Parallel execution has application in several different hardware settings, such as multiple processors in a multicore architecture, or offloading to a separate device in a heterogeneous system. Compilers that support these standards can check for the presence of directives (also known as pragmas) in the source code, and generate parallel code for the specific regions annotated, so they can be run on a specified target device. DawnCC currently supports two standards, OpenACC and OpenMP, but it can easily be extended to support others. You can read more on the subject in the links below:

[OpenACC FAQ](http://www.openacc.org/faq-questions-inline)

[OpenACC MP](http://openmp.org/openmp-faq.html)

In order to use these standards to offload execution to accelerators, it is necessary to compile the modified source code with a compiler that supports the given directive standard (OpenMP 4.0 or OpenACC). In our internal testing environment, we use Portland Group's C Compiler for OpenACC support. You can find out more about it in the following link:

[Portland Group](http://www.pgroup.com/index.htm)

