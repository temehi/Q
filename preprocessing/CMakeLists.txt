# ===========================================================================
#                  SeqAn - The Library for Sequence Analysis
# ===========================================================================
# File: /preprocessing/CMakeLists.txt
#
# CMakeLists.txt file for preprocessing for QChIP.
# ===========================================================================

cmake_minimum_required (VERSION 3.0.0)
project (NexusTools CXX)
message (STATUS "${ColourBold}Configuring preprocessing tools...${ColourReset}")

set (CMAKE_MODULE_PATH
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_MODULE_PATH})

find_package (CXX11 REQUIRED)
find_package (CXX14 REQUIRED)

# ----------------------------------------------------------------------------
# Add Preprocessing targets
# ----------------------------------------------------------------------------

seqan_register_apps ()
