FILE(GLOB SRC
	*.cpp
	Controllers/*.cpp
	CurvesSurfaces/*.cpp
	DataTypes/*.cpp
	Detail/*.cpp
	GlobalEffects/*.cpp
	ImageProcessing/*.cpp
	LocalEffects/*.cpp
	Renderers/*.cpp
	Renderers/${LIBGRAPHICS_RENDERER}/*.cpp
	Renderers/${LIBGRAPHICS_RENDERER_TYPE}/*.cpp
	Resources/*.cpp
	SceneGraph/*.cpp
	ShaderFloats/*.cpp
	Shaders/*.cpp
	Sorting/*.cpp
	Terrain/*.cpp)

FILE(GLOB HEADERS
	*.h
	*.inl
	*/*.h
	*/*.inl
	Renderers/*.h
	Renderers/*.inl
	Renderers/${LIBGRAPHICS_RENDERER}/*.h
	Renderers/${LIBGRAPHICS_RENDERER}/*.inl
	Renderers/${LIBGRAPHICS_RENDERER_TYPE}/*.h
	Renderers/${LIBGRAPHICS_RENDERER_TYPE}/*.inl)

INCLUDE_DIRECTORIES(
	${CORE_INCLUDES}
	${MATHEMATICS_INCLUDES}
	${IMAGICS_INCLUDES}
	${PHYSICS_INCLUDES}
	${GRAPHICS_INCLUDES} ${APPLICATIONS_INCLUDES})

IF (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	ADD_DEFINITIONS (-DWM5_GRAPHICS_DLL_EXPORT=1)
ENDIF ()

IF (LIBGRAPHICS_RENDERER STREQUAL "OpenGLRenderer")
	ADD_DEFINITIONS (-DWM5_USE_OPENGL=1)
ENDIF ()

IF (LIBGRAPHICS_RENDERER STREQUAL "Dx9Renderer")
	ADD_DEFINITIONS (-DWM5_USE_DX9=1)
ENDIF ()

MESSAGE ("Graphics renderer: ${LIBGRAPHICS_RENDERER}")
IF (DEFINED ${LIBGRAPHICS_RENDERER_TYPE})
	MESSAGE ("Graphics renderer type: ${LIBGRAPHICS_RENDERER_TYPE}")
ENDIF ()

ADD_LIBRARY (Wm5Graphics SHARED ${SRC} ${HEADERS})

IF (LIBGRAPHICS_RENDERER STREQUAL "OpenGLRenderer")
	TARGET_LINK_LIBRARIES (Wm5Graphics Wm5Core Wm5Mathematics Wm5Imagics GL)
ENDIF ()

IF (LIBGRAPHICS_RENDERER STREQUAL "Dx9Renderer")
	TARGET_LINK_LIBRARIES (Wm5Graphics Wm5Core Wm5Mathematics Wm5Imagics d3d9 d3dx9 dxerr)
ENDIF ()