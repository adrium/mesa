# Copyright © 2017-2018 Intel Corporation

# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:

# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

vdpau_link_args = []
vdpau_link_depends = []
vdpau_drivers = []
vdpau_driver_dependencies = []

if with_ld_version_script
  vdpau_link_args += ['-Wl,--version-script', join_paths(meson.current_source_dir(), 'vdpau.sym')]
  vdpau_link_depends += files('vdpau.sym')
endif
if with_ld_dynamic_list
  vdpau_link_args += ['-Wl,--dynamic-list', join_paths(meson.current_source_dir(), '../dri-vdpau.dyn')]
  vdpau_link_depends += files('../dri-vdpau.dyn')
endif
foreach d : [[with_gallium_kmsro, driver_kmsro],
             [with_gallium_radeonsi, driver_radeonsi],
             [with_gallium_r300, driver_r300],
             [with_gallium_r600, driver_r600],
             [with_gallium_nouveau, driver_nouveau],
             [with_gallium_freedreno, driver_freedreno],
             [with_gallium_softpipe, driver_swrast],
             [with_gallium_vc4, driver_vc4],
             [with_gallium_v3d, driver_v3d],
             [with_gallium_panfrost, driver_panfrost],
             [with_gallium_etnaviv, driver_etnaviv],
             [with_gallium_tegra, driver_tegra],
             [with_gallium_iris, driver_iris],
             [with_gallium_i915, driver_i915],
             [with_gallium_svga, driver_svga],
             [with_gallium_virgl, driver_virgl],
             [with_gallium_swr, driver_swr],
             [with_gallium_lima, driver_lima],
             [with_gallium_zink, driver_zink]]
  if d[0]
    vdpau_driver_dependencies += d[1]
  endif
endforeach


libvdpau_gallium = shared_library(
  'vdpau_gallium',
  'target.c',
  c_args : c_vis_args,
  cpp_args : cpp_vis_args,
  link_args : [vdpau_link_args, ld_args_gc_sections],
  include_directories : [
    inc_common, inc_util, inc_gallium_winsys, inc_gallium_drivers,
  ],
  link_whole : [libvdpau_st],
  link_with : [
    libgalliumvlwinsys, libgalliumvl, libgallium,
    libpipe_loader_static, libws_null, libwsw, libswdri, libswkmsdri,
  ],
  dependencies : [
    idep_mesautil, idep_xmlconfig_headers,
    vdpau_driver_dependencies,
  ],
  link_depends : vdpau_link_depends,
  soversion : '@0@.@1@.0'.format(VDPAU_MAJOR, VDPAU_MINOR),
  # Will be deleted during installation, see install_megadrivers.py
  install : true,
  install_dir : vdpau_drivers_path,
  name_suffix : 'so',
)
foreach d : [[with_gallium_r300, 'r300'],
             [with_gallium_r600, 'r600'],
             [with_gallium_radeonsi, 'radeonsi'],
             [with_gallium_nouveau, 'nouveau']]
  if d[0]
    vdpau_drivers += 'libvdpau_@0@.so.@1@.@2@.0'.format(d[1], VDPAU_MAJOR, VDPAU_MINOR)
  endif
endforeach

meson.add_install_script(
  install_megadrivers_py.path(),
  libvdpau_gallium.full_path(),
  vdpau_drivers_path,
  vdpau_drivers,
)
