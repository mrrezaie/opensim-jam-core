file(GLOB TEST_PROGS "test*.cpp")
file(GLOB TEST_FILES *.osim *.xml *.sto *.mot *.obj *.vtp *.stl)

OpenSimAddTests(
    TESTPROGRAMS ${TEST_PROGS}
    DATAFILES ${TEST_FILES}
    LINKLIBS osimCommon osimSimulation osimAnalyses osimActuators osimLepton osimMoco osimJAM
    )
