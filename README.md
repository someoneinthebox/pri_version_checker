# PRI file version checker

# Introduction

Sometimes we have one big project which contains a few or a lot of libraries that used as PRI-files. And there may come a moment when you need to numerate each PRI-file to its version. Ofcourse, often we just write a variable in PRI-file which contain a value with version, for example:

`MY_LIB_VERSION = 1.2.0`

But what if we use this library in other projects and someone modify it, and this modification broke compatibility with other project? Answer is simple - when we pull changes from repository we just can't build the project, or worse - there will be not so obvious error that surprise you while project running.

And here is the problem - **how we can dynamically monitor this stuff**?

To answer this question and solve the problem, you can use this simple checker. It works via `QMAKE` and integrates in PRO file like [Replace function](http://doc.qt.io/qt-5/qmake-function-reference.html) through another PRI-file.

# System requirements

[Qt 5](https://www.qt.io/download/)

# Installation

Include `version_checker.pri` into project's PRO-file before any library include:

`include(<path_to_lib>/version_checker.pri)`

# Conditions

Each PRI-file that you want to use with this function must contain a variable named like: `<something>_VERSION` with one value - your custom version.

# How to use?

Version checker has one function: `checkVersion()`. It accepts two parameters:

1. Path to your library PRI-file;
2. Libraries version number that current project uses.

`checkVersion()` **returns** the path to your library PRI-file which means that you can use this function into `include()` code:

`include($$checkVersion(<path_to_lib>/LIB_NAME.pri, 1.0.0))`

If it fails - `QMAKE` will write an error message which version is actual for this library and stop build process.

# Exception

If you have a PRI-file library with another PRI-file attached to it, you must number only attached library version, cause, as it often happens, included library is a child of attached library inside it. So the correct using of checker will be:

_PARENT_LIB_NAME.pri:_

`LIB_NAME_VERSION = 1.0.0`

_CHILD_LIB_NAME.pri:_

`include(<path_to_lib>/PARENT_LIB_NAME.pri)`

_Your PRO-file:_

    $$checkVersion(<path_to_lib>/PARENT_LIB_NAME.pri, 1.0.0)
    include(<path_to_lib>/CHILD_LIB_NAME.pri)
