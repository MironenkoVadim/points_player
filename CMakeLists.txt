# Минимальная версия CMake, требуемая для сборки проекта
cmake_minimum_required(VERSION 3.6 FATAL_ERROR)

# Предпочтительно следовать стандартам принятым в указанном диапазоне версий
cmake_policy(VERSION 3.6..3.7)

# Название проекта
project(points_play VERSION 0.1.0 LANGUAGES C CXX)

# Имя и почта автора (требуются для создания пакетов)
set(${PROJECT_NAME}_AUTHOR_NAME "Vadim Mironenko")
set(${PROJECT_NAME}_AUTHOR_EMAIL "mironenkovadim1997@gmail.com")

# Рекомендуемый способ подключения MyxCMake
include(cmake/myx_setup.cmake)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_EXTENSIONS ON)

# Поиск компонентов Qt
if(NOT QT_DEFAULT_MAJOR_VERSION)
  set(QT_DEFAULT_MAJOR_VERSION "5")
endif()
myx_find_qt(COMPONENTS Core Gui Widgets REQUIRED)

# Qwt
find_package(Qwt REQUIRED)

# Цель для создания исполняемого файла
myx_add_executable(${PROJECT_NAME})

# Настройка свойств цели
myx_target_setup(${PROJECT_NAME}
  CPP
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/main.cpp
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/main_window.cpp
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/marks_plot.cpp
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/points_player.cpp
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/qwt_plot_picker_ext.cpp
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/qwt_plot_extcurve.cpp
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/qwt_series_data_ext.cpp
  PRIVATE_HEADERS
    ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/qwt_point_ext.hpp
    ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/qwt_plot_extcurve.hpp
    ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/qwt_series_data_ext.hpp
  LINK_LIBRARIES
  Qwt::Qwt
)

# Настройка Qt для цели
myx_target_setup_qt(${PROJECT_NAME}
  PRIVATE_MOC
    ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/main_window.hpp
    ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/marks_plot.hpp
    ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/points_player.hpp
    ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME}/qwt_plot_picker_ext.hpp
  UI
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/main_window.ui
    ${PROJECT_SOURCE_DIR}/src/${PROJECT_NAME}/points_player.ui
  QRC
    ${PROJECT_SOURCE_DIR}/files/share/icon.qrc
  COMPONENTS Core Gui Widgets
  PRIVATE Core)

# Автоматически генерируемый файл с информацией о репозитории
myx_generate_git_info_header(${PROJECT_NAME} "git_info_p.hpp")

# Автоматически генерируемый приватный заголовочный файл
myx_generate_private_config_header(${PROJECT_NAME} "config_p.hpp")

# Форматирование исходных текстов с помощью uncrustify
myx_uncrustify(${PROJECT_NAME})

# Формирование документации
myx_doc_doxygen(${PROJECT_NAME} HTML YES)

# Подключение функций для сопровождения проекта
if(PROJECT_IS_TOP_LEVEL)
  if(MyxxCMake_DIR)
    myxx()
  endif()
endif()
