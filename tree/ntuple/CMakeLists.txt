# Copyright (C) 1995-2020, Rene Brun and Fons Rademakers.
# All rights reserved.
#
# For the licensing terms see $ROOTSYS/LICENSE.
# For the list of contributors see $ROOTSYS/README/CREDITS.

############################################################################
# CMakeLists.txt file for building ROOT ntuple package
# @author Jakob Blomer CERN
############################################################################

if(NOT root7)
  return()
endif()

ROOT_STANDARD_LIBRARY_PACKAGE(ROOTNTuple
HEADERS
  ROOT/RCluster.hxx
  ROOT/RClusterPool.hxx
  ROOT/RColumn.hxx
  ROOT/RColumnElementBase.hxx
  ROOT/REntry.hxx
  ROOT/RField.hxx
  ROOT/RFieldBase.hxx
  ROOT/RFieldToken.hxx
  ROOT/RFieldUtils.hxx
  ROOT/RFieldVisitor.hxx
  ROOT/RMiniFile.hxx
  ROOT/RNTuple.hxx
  ROOT/RNTupleDescriptor.hxx
  ROOT/RNTupleFillContext.hxx
  ROOT/RNTupleFillStatus.hxx
  ROOT/RNTupleImtTaskScheduler.hxx
  ROOT/RNTupleJoinTable.hxx
  ROOT/RNTupleMerger.hxx
  ROOT/RNTupleMetrics.hxx
  ROOT/RNTupleModel.hxx
  ROOT/RNTupleParallelWriter.hxx
  ROOT/RNTupleProcessor.hxx
  ROOT/RNTupleRange.hxx
  ROOT/RNTupleReadOptions.hxx
  ROOT/RNTupleReader.hxx
  ROOT/RNTupleSerialize.hxx
  ROOT/RNTupleUtil.hxx
  ROOT/RNTupleView.hxx
  ROOT/RNTupleWriteOptions.hxx
  ROOT/RNTupleWriteOptionsDaos.hxx
  ROOT/RNTupleWriter.hxx
  ROOT/RNTupleZip.hxx
  ROOT/RPage.hxx
  ROOT/RPageAllocator.hxx
  ROOT/RPageNullSink.hxx
  ROOT/RPagePool.hxx
  ROOT/RPageSinkBuf.hxx
  ROOT/RPageStorage.hxx
  ROOT/RPageStorageFile.hxx
  ROOT/RRawPtrWriteEntry.hxx
SOURCES
  v7/src/RCluster.cxx
  v7/src/RClusterPool.cxx
  v7/src/RColumn.cxx
  v7/src/RColumnElement.cxx
  v7/src/RField.cxx
  v7/src/RFieldBase.cxx
  v7/src/RFieldMeta.cxx
  v7/src/RFieldSequenceContainer.cxx
  v7/src/RFieldUtils.cxx
  v7/src/RFieldVisitor.cxx
  v7/src/RMiniFile.cxx
  v7/src/RNTuple.cxx
  v7/src/RNTupleDescriptor.cxx
  v7/src/RNTupleDescriptorFmt.cxx
  v7/src/RNTupleFillContext.cxx
  v7/src/RNTupleJoinTable.cxx
  v7/src/RNTupleMerger.cxx
  v7/src/RNTupleMetrics.cxx
  v7/src/RNTupleModel.cxx
  v7/src/RNTupleParallelWriter.cxx
  v7/src/RNTupleProcessor.cxx
  v7/src/RNTupleReader.cxx
  v7/src/RNTupleSerialize.cxx
  v7/src/RNTupleUtil.cxx
  v7/src/RNTupleWriteOptions.cxx
  v7/src/RNTupleWriter.cxx
  v7/src/RNTupleView.cxx
  v7/src/RPage.cxx
  v7/src/RPageAllocator.cxx
  v7/src/RPagePool.cxx
  v7/src/RPageSinkBuf.cxx
  v7/src/RPageStorage.cxx
  v7/src/RPageStorageFile.cxx
LINKDEF
  LinkDef.h
DEPENDENCIES
  Imt
  RIO
  ROOTVecOps
)

target_link_libraries(ROOTNTuple PRIVATE xxHash::xxHash)

# Enable RNTuple support for Intel DAOS
if(daos OR daos_mock)
  set(ROOTNTuple_EXTRA_HEADERS ROOT/RPageStorageDaos.hxx)
  target_sources(ROOTNTuple PRIVATE v7/src/RDaos.cxx v7/src/RPageStorageDaos.cxx)
  target_compile_definitions(ROOTNTuple PRIVATE R__ENABLE_DAOS)

  target_include_directories(ROOTNTuple PRIVATE ${UUID_INCLUDE_DIR})
  target_link_libraries(ROOTNTuple PRIVATE ${UUID_LIBRARY})
  if(daos_mock)
    target_include_directories(ROOTNTuple PRIVATE v7/inc/ROOT/libdaos_mock/)
    target_sources(ROOTNTuple PRIVATE v7/src/libdaos_mock/libdaos_mock.cxx)
  else()
    target_include_directories(ROOTNTuple PRIVATE ${DAOS_INCLUDE_DIRS})
    target_link_libraries(ROOTNTuple PRIVATE ${DAOS_LIBRARIES})
  endif()
endif()

if(MSVC)
  target_compile_definitions(ROOTNTuple PRIVATE _USE_MATH_DEFINES)
  set_source_files_properties(v7/src/RField.cxx PROPERTIES COMPILE_FLAGS /bigobj)
endif()

ROOT_ADD_TEST_SUBDIRECTORY(v7/test)
