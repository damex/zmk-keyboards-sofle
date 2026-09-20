if(CONFIG_ZMK_SPLIT_ESB)
  execute_process(
    COMMAND west patch -sm zmk-feature-split-esb clean
    WORKING_DIRECTORY ${WEST_TOPDIR}
    OUTPUT_QUIET ERROR_QUIET
  )
  execute_process(
    COMMAND west patch -sm zmk-feature-split-esb apply
    WORKING_DIRECTORY ${WEST_TOPDIR}
    RESULT_VARIABLE patch_result
  )
  if(NOT patch_result EQUAL 0)
    message(FATAL_ERROR "west patch apply for zmk-feature-split-esb failed (${patch_result})")
  endif()
endif()

if(CONFIG_BOOTLOADER_MCUBOOT)
  execute_process(
    COMMAND ${PYTHON_EXECUTABLE} -c
            "import sysconfig; print(sysconfig.get_paths()['purelib'])"
    OUTPUT_VARIABLE python_purelib
    OUTPUT_STRIP_TRAILING_WHITESPACE
  )
  set(pip_pyz ${CMAKE_BINARY_DIR}/pip.pyz)
  if(NOT EXISTS ${pip_pyz})
    file(DOWNLOAD https://bootstrap.pypa.io/pip/pip.pyz ${pip_pyz}
         STATUS pip_download_status)
    list(GET pip_download_status 0 pip_download_code)
    if(NOT pip_download_code EQUAL 0)
      message(FATAL_ERROR "pip.pyz download failed: ${pip_download_status}")
    endif()
  endif()
  execute_process(
    COMMAND ${PYTHON_EXECUTABLE} ${pip_pyz} install --quiet --target ${python_purelib}
            -r ${WEST_TOPDIR}/bootloader/mcuboot/scripts/requirements.txt
    RESULT_VARIABLE pip_result
  )
  if(NOT pip_result EQUAL 0)
    message(FATAL_ERROR "pip install for mcuboot requirements failed (${pip_result})")
  endif()
endif()
