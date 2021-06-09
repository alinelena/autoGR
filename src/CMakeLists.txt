add_library(sphnf
control_file.f90
find_kgrids.f90
grid_utils.f90
niggli.f90
sp_hnfs.f90
)
target_include_directories(sphnf PUBLIC  ${KGRID_INC})

#add_executable(lat_type.x
#lat_id_driver.f90
#)
#target_include_directories(lat_type.x PUBLIC  ${KGRID_INC})
#target_link_libraries(lat_type.x PRIVATE sphnf ${LIBS})



add_executable(kpoints.x
driver.f90
)
target_include_directories(kpoints.x PUBLIC  ${KGRID_INC})
target_link_libraries(kpoints.x PRIVATE sphnf ${LIBS})

add_executable(snf.x
driver2.f90
)
target_include_directories(snf.x PUBLIC  ${KGRID_INC})
target_link_libraries(snf.x PRIVATE sphnf ${LIBS})
