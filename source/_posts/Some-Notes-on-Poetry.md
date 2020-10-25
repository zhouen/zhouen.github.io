---
layout: post
title: Some Notes on Poetry
sitemap: false
date: 2020-10-25 22:56:41
categories:
tags: python
---

## Install `poetry` on Ubuntu 20.04
```sh
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -
```
REF: [https://python-poetry.org/docs/#installation](https://python-poetry.org/docs/#installation)

## Error when starting `poetry shell`
```sh
ModuleNotFoundError: No module named 'virtualenv.seed.via_app_data'
```
This is due to the `virtualenv` installed in the system, it can be solved by removing it:
```sh
sudo apt remove python3-virtualenv
```

## Add packages from existing `requirements.txt`
Sometimes you already have a project and `reqirements.txt` setup, but you wantted to start using `poetry` to manage your dependencies. Here are the steps to add the existing dependencies from `requirements.txt` to `pyproject.toml` that generate by `poetry`.
-   Start by generate a `pyproject.toml` file for `poetry`:
    ```sh
    poetry init
    ```
-   In the directory where both `requirement.txt` and `pyproject.toml` are stored, run this command:
    ```sh
    cat requirements.txt | perl -pe 's/([<=>]+)/:$1/' | xargs -t -n 1 -I {} poetry add '{}'
    ```
