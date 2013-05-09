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

IF (LIBGRAPHICS_RENDERER STREQUAL "OpenGLRenderer")
	ADD_DEFINITIONS (-DWM5_USE_OPENGL=1)
ENDIF ()

IF (LIBGRAPHICS_RENDERER STREQUAL "Dx9Renderer")
	ADD_DEFINITIONS (-DWM5_USE_DX9=1)
ENDIF ()

MESSAGE ("Graphics renderer: ${LIBGRAPHICS_RENDERER} type: ${LIBGRAPHICS_RENDERER_TYPE}")

ADD_LIBRARY (Wm5Graphics SHARED ${SRC})
TARGET_LINK_LIBRARIES (Wm5Graphics GL)
