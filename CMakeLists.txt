cmake_minimum_required(VERSION 3.0.0)
project(LSH VERSION 1.0.2)
set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})

set(LSH_ARCH "Unknown")
if(${CMAKE_GENERATOR_PLATFORM})
    set(LSH_ARCH ${CMAKE_GENERATOR_PLATFORM})
else()
    if(${CMAKE_C_COMPILER} MATCHES "x86_64")
        set(LSH_ARCH "x64")
    elseif(${CMAKE_C_COMPILER} MATCHES "x86")
        set(LSH_ARCH "x86")
    elseif(${CMAKE_C_COMPILER} MATCHES "arm")
        set(LSH_ARCH "arm")
    elseif(${CMAKE_SYSTEM_PROCESSOR} MATCHES "AMD64")
        set(LSH_ARCH "x64")
    else()
        set(LSH_ARCH ${CMAKE_SYSTEM_PROCESSOR})
    endif()
endif()

if(${LSH_ARCH} MATCHES "x64" OR ${LSH_ARCH} MATCHES "x86")
	if(NOT MSVC)
		add_compile_options(-mssse3)
		add_compile_options(-msse2)
		add_compile_options(-mavx2)
	else()
		add_compile_options(/arch:AVX2)
	endif()
	set(SRCS 
		src/x86_64/cpu_info_ia32.c
		src/x86_64/hmac.c
		src/x86_64/lsh.c
		src/x86_64/no_arch/lsh256.c
		src/x86_64/no_arch/lsh512.c
		src/x86_64/avx2/lsh256_avx2.c
		src/x86_64/avx2/lsh512_avx2.c
		src/x86_64/ssse3/lsh256_ssse3.c
		src/x86_64/ssse3/lsh512_ssse3.c
		src/x86_64/sse2/lsh256_sse2.c
		src/x86_64/sse2/lsh512_sse2.c
		src/x86_64/xop/lsh256_xop.c
		src/x86_64/xop/lsh512_xop.c
	)
elseif(${LSH_ARCH} MATCHES "arm")
set(SRCS
	src/general/hmac.c
	src/general/lsh.c
	src/general/lsh256.c
	src/general/lsh512.c
)
else()
set(SRCS
	src/general/hmac.c
	src/general/lsh.c
	src/general/lsh256.c
	src/general/lsh512.c
)
endif()

add_library(lsh ${SRCS})