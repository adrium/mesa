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

va_link_args = []
va_link_depends = []
va_drivers = []
va_driver_dependencies = []

if with_ld_version_script
  va_link_args += ['-Wl,--version-script', join_paths(meson.current_source_dir(), 'va.sym')]
  va_link_depends += files('va.sym')
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
    va_driver_dependencies += d[1]
  endif
endforeach

libva_gallium = shared_library(
  'gallium_drv_video',
  'target.c',
  c_args : c_vis_args,
  cpp_args : cpp_vis_args,
  link_args : [va_link_args, ld_args_gc_sections],
  include_directories : [
    inc_common, inc_util, inc_gallium_winsys, inc_gallium_drivers,
  ],
  link_whole : [libva_st],
  link_with : [
    libgalliumvlwinsys, libgalliumvl, libgallium,
    libpipe_loader_static, libws_null, libwsw, libswdri, libswkmsdri,
  ],
  dependencies : [
    dep_libdrm, idep_mesautil, idep_xmlconfig_headers,
    va_driver_dependencies,
  ],
  link_depends : va_link_depends,
  # Will be deleted during installation, see install_megadrivers.py
  install : true,
  install_dir : va_drivers_path,
  name_suffix : 'so',
)

foreach d : [[with_gallium_r600, 'r600'],
             [with_gallium_radeonsi, 'radeonsi'],
             [with_gallium_nouveau, 'nouveau']]
  if d[0]
    va_drivers += '@0@_drv_video.so'.format(d[1])
  endif
endforeach

meson.add_install_script(
  install_megadrivers_py.path(),
  libva_gallium.full_path(),
  va_drivers_path,
  va_drivers,
)
