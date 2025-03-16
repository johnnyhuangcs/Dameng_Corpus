

# dmPython 简介

本文档主要介绍 DM 数据库的 Python 接口 dmPython，包括了 dmPython 的安装、dmPython 接口、django_dmPython 驱动、SQLAlchemy_dm 方言包、DBUtils 包的相关说明。

dmPython 是 DM 依据 Python DB API version 2.0 中 API 使用规定开发的数据库访问接口，Python 应用程序能够通过这些 API 对 DM 数据库进行访问。因 dmPython 通过调用 DM DPI 接口完成 Python 模块扩展，在使用过程中，除 Python 标准库以外，还需要 DPI 的运行环境。

后文中依此介绍了如何安装 dmPython，如何使用 dmPython 支持的接口，如何使用 django_dmPython 驱动建立 Django 和 DM 数据库的连接，如何使用 SQLAlchemy_dm 方言包建立 SQLAlchemy 与 DM 数据库连接，及如何使用 DBUtils 包进行 DM 数据库连接池的管理。
