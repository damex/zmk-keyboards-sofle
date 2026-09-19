if(CONFIG_BOOTLOADER_MCUBOOT)
  execute_process(
    COMMAND ${PYTHON_EXECUTABLE} -m pip install --quiet
            -r ${WEST_TOPDIR}/bootloader/mcuboot/scripts/requirements.txt
    RESULT_VARIABLE pip_result
  )
  if(NOT pip_result EQUAL 0)
    message(FATAL_ERROR "pip install for mcuboot requirements failed (${pip_result})")
  endif()
endif()
