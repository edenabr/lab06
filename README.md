# Lab06. Пакетирование проекта с помощью CPack
---

Данные)
```bash
export GITHUB_USERNAME=edenabr
export GITHUB_EMAIL=edenabramova@gmail.com
alias gsed=sed
```
Команда:

```bash
alias gsed=sed
```

создаёт алиас `gsed`, чтобы команды из tutorial работали в Linux.


---
Создание проекта lab06 из lab05


```bash
cd "/home/vboxuser/Рабочий стол/project/projects"

rm -rf lab06

git clone https://github.com/${GITHUB_USERNAME}/lab05 lab06

cd lab06
```
---
Настройка удалённого репозитория
```bash
git remote remove origin

git remote add origin https://github.com/${GITHUB_USERNAME}/lab06

git config --global user.name "edenabr"

git config --global user.email "edenabramova@gmail.com"

git branch -M master
```
---
Добавление версии проекта в CMakeLists.txt

```bash
gsed -i '/project(print)/a\
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")
' CMakeLists.txt
```

Эта команда добавляет в `CMakeLists.txt` строку:

```cmake
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")
```

Она создаёт строковое представление версии проекта с буквой `v`, например:

```text
v0.1.0.0
```

---

```bash
gsed -i '/project(print)/a\
set(PRINT_VERSION\
  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
' CMakeLists.txt
```

---

```bash
gsed -i '/project(print)/a\
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
```


---

```bash
gsed -i '/project(print)/a\
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
```


---

```bash
gsed -i '/project(print)/a\
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
```


---

```bash
gsed -i '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
```


В результате в проект была добавлена версия:

```text
0.1.0.0
```

Эта версия дальше используется CPack при создании пакета.

---
Создание файла DESCRIPTION


```bash
cat > DESCRIPTION <<'EOF'
Static C++ library for printing text to output streams.
EOF
```

Был создан файл `DESCRIPTION`.

Он содержит краткое описание проекта:

```text
Static C++ library for printing text to output streams.
```


---
Создание ChangeLog.md


```bash
touch ChangeLog.md

export DATE="`LANG=en_US date +'%a %b %d %Y'`"
```

Команда:

---

```bash
cat > ChangeLog.md <<EOF
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```

Эта команда записывает в `ChangeLog.md` информацию о первой версии пакета.

В файл добавляется:

```text
дата
имя пользователя GitHub
email
версия пакета
описание релиза
```
---
Создание CPackConfig.cmake

```bash
cat > CPackConfig.cmake <<EOF
include(InstallRequiredSystemLibraries)
EOF
```

Создаётся файл `CPackConfig.cmake`.
---
Добавление основных параметров пакета

```bash
cat >> CPackConfig.cmake <<EOF
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
EOF
```

---


Настройка RPM-пакета

```bash
cat >> CPackConfig.cmake <<EOF

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```
---
Настройка DEB-пакета


```bash
cat >> CPackConfig.cmake <<EOF

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```

---
Подключение CPack

```bash
cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF
```

Строка:

```cmake
include(CPack)
```

подключает модуль CPack.

```bash
cpack
```

---
Подключение CPackConfig.cmake к CMakeLists.txt


```bash
cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```

---

Создание коммита


```bash
git add .

git commit -m "added cpack config"

git tag v0.1.0.0
```

---

Создание архива через cpack
```bash
cd _build

cpack -G "TGZ"

cd ..
```


---
Создание пакета через цель package


```bash
cmake -H. -B_build -DCPACK_GENERATOR="TGZ"

cmake --build _build --target package
```

Команда:

```bash
cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
```

повторно конфигурирует проект и указывает, что CPack должен использовать генератор `TGZ`.


---
Сохранение артефактов

```bash
mkdir -p artifacts

mv _build/*.tar.gz artifacts

tree artifacts
```

Команда:

```bash
mkdir -p artifacts
```

создаёт директорию `artifacts`.

Команда:

```bash
mv _build/*.tar.gz artifacts
```

перемещает созданные архивы `.tar.gz` из директории `_build` в папку `artifacts`.

Команда:

```bash
tree artifacts
```
---

Далее отправка)
