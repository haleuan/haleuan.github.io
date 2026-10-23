+++
categories = ['Python']
comments = false
date = '2024-09-28T20:19:31+08:00'
draft = true
tags = ['Python', 'VSCode']
title = 'Importing a Parent-Directory Package in VS Code Without Using sys.path'
+++

I recently worked on a Python project with the following structure (only the `src` and `test` directories matter here):

```bash
smartQA
├── readme.md
├── requirements.txt
├── src
│   ├── __init__.py
│   ├── api
│   │   ├── __init__.py
│   │   └── app.py
│   ├── backend
│   │   ├── __init__.py
│   │   └── models_config.py
│   └── utils
│       ├── __init__.py
│       ├── db.py
│       ├── model_load.py
│       └── promot.py
└── tests
    ├── __init__.py
    ├── test.py
    ├── test_notebook
    │   ├── __init__.py
    │   └── test_api.ipynb
    └── visualized_response.json
```

I wanted to write `from src.utils.db` in `tests/test.py`, but the interpreter reported `No module named 'src'`. In other words, it could not find the package because the environment did not include the other directories when `test.py` ran. I found two approaches online:

1. Use `sys.path()`.
2. Create a `.vscode/launch.json` file in the project directory.

I felt the first approach was not very conventional, while the second added an unnecessary file, so I looked for a cleaner option.

In VS Code, search settings for `terminal.integrated.env` and set it to `${workspaceFolder}` for the relevant operating system.

I also recommend changing `python.analysis.extraPaths` so that code completion works and the editor does not warn that the module cannot be found.

```json
    # Example for macOS
	"terminal.integrated.env.osx": {
		"PYTHONPATH": "${workspaceFolder}",
	},
```

Similarly, to make Jupyter in VS Code search the same package path, search settings for `jupyter.notebookFileRoot`, set it to `${workspaceFolder}`, and **restart VS Code**.

> **Translation note:** This English version was translated with the assistance of an LLM.
