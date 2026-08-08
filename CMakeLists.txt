cmake_minimum_required(VERSION 3.20) ## Set arbiratrily for now.
project(firmware-os LANGUAGES C) ## Specifies the project name, and compiling C

set(CMAKE_C_STANDARD 11)


add_executable(kernel.elf
    kernel/kmain.c
) #Creates target (kernel.elf), every C file is listed here 

target_compile_options(kernel.elf PRIVATE
    -freestanding #no libc. check docs/learning&design_choices.md
    -fno-stack-protector #ths is a kernel, so we don't want stack protection unless infra is well built
    -fno-pic #simpler addresses - disables generation of position independant code
)