# SPDX-License-Identifier: BSD-3-Clause
# Copyright 2022 Intel Corporation

project('mtl_plugins', 'c', default_options: ['buildtype=release'],
        version: run_command(find_program('cat'), files('../VERSION'), check: true).stdout().strip(),)

#detect os
exec_env = host_machine.system()
set_variable('is_windows', exec_env == 'windows')

# allow experimental api
add_global_arguments('-DALLOW_EXPERIMENTAL_API', language : 'c')

cc = meson.get_compiler('c')

mtl_dep = dependency('mtl', required : true)
libpthread_dep = cc.find_library('pthread', required : true)

# add source file
subdir('sample')

plugins_c_args = []
plugins_ld_args = []

# enable warning as error for non debug build
if get_option('buildtype') != 'debug'
  plugins_c_args += ['-Werror']
endif
plugins_c_args += ['-Wall']
plugins_c_args += ['-Wunused-parameter']

#simd build option, enable sse4.2 default, todo: do we need AVX2/AVX512 for app ?
if host_machine.cpu_family() == 'x86' or host_machine.cpu_family() == 'x86_64'
  plugins_c_args += ['-msse4.2']
endif

if is_windows
  ws2_32_dep = cc.find_library('ws2_32', required: true)
else
  ws2_32_dep = [] # add this when the code uses hton/ntoh
endif

# default no asan dep
asan_dep = []
if get_option('enable_asan') == true
  message('Enable -fsanitize=address')
  plugins_c_args += ['-fsanitize=address']
  asan_dep = cc.find_library('asan', required : true)
endif

# build plugins lib
shared_library('st_plugin_st22_sample', plugins_st22_sample_sources,
  c_args : plugins_c_args,
  link_args : plugins_ld_args,
  # asan should be always the first dep
  dependencies: [asan_dep, mtl_dep, libpthread_dep, ws2_32_dep],
  install: true
)

shared_library('st_plugin_convert_sample', plugins_convert_sample_sources,
  c_args : plugins_c_args,
  link_args : plugins_ld_args,
  # asan should be always the first dep
  dependencies: [asan_dep, mtl_dep, libpthread_dep, ws2_32_dep],
  install: true
)
