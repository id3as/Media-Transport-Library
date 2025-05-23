# SPDX-License-Identifier: BSD-3-Clause
# Copyright 2023 Intel Corporation

udp_preload_c_args = []
# enable warning as error for non debug build
if get_option('buildtype') != 'debug'
  udp_preload_c_args += ['-Werror']
endif
udp_preload_c_args += ['-Wall']
udp_preload_c_args += ['-Wunused-parameter']

# simd build option, enable sse4.2 default, todo: do we need AVX2/AVX512 ?
if host_machine.cpu_family() == 'x86' or host_machine.cpu_family() == 'x86_64'
  udp_preload_c_args += ['-msse4.2']
endif

udp_preload_ld_args = []

udp_preload_sources = files('udp_preload.c')

# default no asan dep
asan_dep = []
if get_option('enable_asan') == true
  message('Enable -fsanitize=address')
  udp_preload_c_args += ['-fsanitize=address']
  asan_dep = cc.find_library('asan', required : true)
endif

# build mtl udp preload lib
shared_library('mtl_udp_preload', udp_preload_sources,
  c_args : udp_preload_c_args,
  link_args : udp_preload_ld_args,
  # asan should be always the first dep
  dependencies: [asan_dep, mtl_dep, libdl_dep],
  install: true
)
