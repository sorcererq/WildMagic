CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

PROJECT(WildMagic5)

#############################################
## Compiler
IF (MSVC)
ELSE ()
	SET (CMAKE_C_FLAGS                "-Wall -std=c99")
	SET (CMAKE_C_FLAGS_DEBUG          "-g")
	SET (CMAKE_C_FLAGS_MINSIZEREL     "-Os -DNDEBUG")
	SET (CMAKE_C_FLAGS_RELEASE        "-O4 -DNDEBUG")
	SET (CMAKE_C_FLAGS_RELWITHDEBINFO "-O2 -g")
	
	SET (CMAKE_CXX_FLAGS                "-Wall")
	SET (CMAKE_CXX_FLAGS_DEBUG          "-g")
	SET (CMAKE_CXX_FLAGS_MINSIZEREL     "-Os -DNDEBUG")
	SET (CMAKE_CXX_FLAGS_RELEASE        "-O4 -DNDEBUG")
	SET (CMAKE_CXX_FLAGS_RELWITHDEBINFO "-O2 -g")
ENDIF ()

#############################################
## CONFIGURATION

SET (WM5_ROOT_DIR ${CMAKE_BINARY_DIR})

SET (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${WM5_ROOT_DIR})
SET (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${WM5_ROOT_DIR})
SET (CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${WM5_ROOT_DIR})

## select the build type
SET(CMAKE_BUILD_TYPE Release)
#SET(CMAKE_BUILD_TYPE Debug)

## set other build definitions

IF (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
	SET (LIBAPPLICATIONS_TYPE GlxApplication)
	SET (LIBGRAPHICS_RENDERER_TYPE GlxRenderer)
	SET (LIBGRAPHICS_RENDERER OpenGLRenderer)
ENDIF ()

IF (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	SET(LIBAPPLICATIONS_TYPE WinApplication)
	IF (${WM5_RENDERER} MATCHES "DX9")
		SET (LIBGRAPHICS_RENDERER Dx9Renderer)
	ELSE ()
		SET(LIBGRAPHICS_RENDERER OpenGLRenderer)
		SET(LIBGRAPHICS_RENDERER_TYPE WglRenderer)
	ENDIF ()
ENDIF ()

## LibApplications type setup
#SET(LIBAPPLICATIONS_TYPE AglApplication)
#SET(LIBAPPLICATIONS_TYPE GlutApplication)

## LibGraphics type setup
#

#SET(LIBGRAPHICS_RENDERER_TYPE AglRenderer)
#SET(LIBGRAPHICS_RENDERER_TYPE GlutRenderer)
#SET(LIBGRAPHICS_RENDERER_TYPE WglRenderer)

#############################################
## BUILDING

SET(CORE_INCLUDES
	${CMAKE_SOURCE_DIR}/LibCore/
	${CMAKE_SOURCE_DIR}/LibCore/Assert
	${CMAKE_SOURCE_DIR}/LibCore/DataTypes
	${CMAKE_SOURCE_DIR}/LibCore/InputOutput
	${CMAKE_SOURCE_DIR}/LibCore/Memory
	${CMAKE_SOURCE_DIR}/LibCore/ObjectSystems
	${CMAKE_SOURCE_DIR}/LibCore/Threading
	${CMAKE_SOURCE_DIR}/LibCore/Time)
ADD_SUBDIRECTORY(LibCore bin/LibCore)

SET(MATHEMATICS_INCLUDES
	${CMAKE_SOURCE_DIR}/LibMathematics
	${CMAKE_SOURCE_DIR}/LibMathematics/Algebra
	${CMAKE_SOURCE_DIR}/LibMathematics/Approximation
	${CMAKE_SOURCE_DIR}/LibMathematics/Base
	${CMAKE_SOURCE_DIR}/LibMathematics/ComputationalGeometry
	${CMAKE_SOURCE_DIR}/LibMathematics/Containment
	${CMAKE_SOURCE_DIR}/LibMathematics/CurvesSurfacesVolumes
	${CMAKE_SOURCE_DIR}/LibMathematics/Distance
	${CMAKE_SOURCE_DIR}/LibMathematics/Interpolation
	${CMAKE_SOURCE_DIR}/LibMathematics/Intersection
	${CMAKE_SOURCE_DIR}/LibMathematics/Meshes
	${CMAKE_SOURCE_DIR}/LibMathematics/Miscellaneous
	${CMAKE_SOURCE_DIR}/LibMathematics/NumericalAnalysis
	${CMAKE_SOURCE_DIR}/LibMathematics/Objects2D
	${CMAKE_SOURCE_DIR}/LibMathematics/Objects3D
	${CMAKE_SOURCE_DIR}/LibMathematics/Query
	${CMAKE_SOURCE_DIR}/LibMathematics/Rational)
ADD_SUBDIRECTORY(LibMathematics bin/LibMathematics)

SET(IMAGICS_INCLUDES
	${CMAKE_SOURCE_DIR}/LibImagics/
	${CMAKE_SOURCE_DIR}/LibImagics/BinaryOperations
	${CMAKE_SOURCE_DIR}/LibImagics/Extraction
	${CMAKE_SOURCE_DIR}/LibImagics/Filters
	${CMAKE_SOURCE_DIR}/LibImagics/Images
	${CMAKE_SOURCE_DIR}/LibImagics/RasterDrawing
	${CMAKE_SOURCE_DIR}/LibImagics/Segmenters)
ADD_SUBDIRECTORY(LibImagics bin/LibImagics)

SET(PHYSICS_INCLUDES
	${CMAKE_SOURCE_DIR}/LibPhysics/
	${CMAKE_SOURCE_DIR}/LibPhysics/CollisionDetection
	${CMAKE_SOURCE_DIR}/LibPhysics/Fluid
	${CMAKE_SOURCE_DIR}/LibPhysics/Intersection
	${CMAKE_SOURCE_DIR}/LibPhysics/LCPSolver
	${CMAKE_SOURCE_DIR}/LibPhysics/ParticleSystem
${CMAKE_SOURCE_DIR}/LibPhysics/RigidBody)
ADD_SUBDIRECTORY(LibPhysics bin/LibPhysics)

IF(DEFINED LIBGRAPHICS_RENDERER)
SET(GRAPHICS_INCLUDES
	${CMAKE_SOURCE_DIR}/LibGraphics
	${CMAKE_SOURCE_DIR}/LibGraphics/Controllers
	${CMAKE_SOURCE_DIR}/LibGraphics/CurvesSurfaces
	${CMAKE_SOURCE_DIR}/LibGraphics/DataTypes
	${CMAKE_SOURCE_DIR}/LibGraphics/Detail
	${CMAKE_SOURCE_DIR}/LibGraphics/GlobalEffects
	${CMAKE_SOURCE_DIR}/LibGraphics/ImageProcessing
	${CMAKE_SOURCE_DIR}/LibGraphics/LocalEffects
	${CMAKE_SOURCE_DIR}/LibGraphics/Renderers
	${CMAKE_SOURCE_DIR}/LibGraphics/Renderers/${LIBGRAPHICS_RENDERER}
	${CMAKE_SOURCE_DIR}/LibGraphics/Renderers/${LIBGRAPHICS_RENDERER_TYPE}
	${CMAKE_SOURCE_DIR}/LibGraphics/Resources
	${CMAKE_SOURCE_DIR}/LibGraphics/SceneGraph
	${CMAKE_SOURCE_DIR}/LibGraphics/ShaderFloats
	${CMAKE_SOURCE_DIR}/LibGraphics/Shaders
	${CMAKE_SOURCE_DIR}/LibGraphics/Sorting
	${CMAKE_SOURCE_DIR}/LibGraphics/Terrain)
ADD_SUBDIRECTORY(LibGraphics bin/LibGraphics)
ENDIF(DEFINED LIBGRAPHICS_RENDERER)

IF(DEFINED LIBAPPLICATIONS_TYPE)
SET(APPLICATIONS_INCLUDES
	${CMAKE_SOURCE_DIR}/LibApplications
	${CMAKE_SOURCE_DIR}/LibApplications/${LIBAPPLICATIONS_TYPE})
ADD_SUBDIRECTORY(LibApplications bin/LibApplications)
ENDIF(DEFINED LIBAPPLICATIONS_TYPE)

## Samples

FUNCTION (ADD_SAMPLE name)
	
	FILE(GLOB SRC ${name}/*.cpp)
	FILE(GLOB HEADERS ${name}/*.h ${name}/*.inl)

	INCLUDE_DIRECTORIES (
		${CORE_INCLUDES}
		${MATHEMATICS_INCLUDES}
		${IMAGICS_INCLUDES}
		${PHYSICS_INCLUDES}
		${GRAPHICS_INCLUDES}
		${APPLICATIONS_INCLUDES})

	ADD_EXECUTABLE (${name} ${SRC} ${HEADERS})
	TARGET_LINK_LIBRARIES (${name} Wm5Core Wm5Applications Wm5Graphics Wm5Imagics Wm5Mathematics Wm5Physics)
ENDFUNCTION ()

ADD_SUBDIRECTORY (SampleGraphics)
ADD_SUBDIRECTORY (SampleImagics)
ADD_SUBDIRECTORY (SampleMathematics)
ADD_SUBDIRECTORY (SamplePhysics)