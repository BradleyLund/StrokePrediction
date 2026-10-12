# Tools: Python, Notebooks and Environments

## Python libraries we'll use
| Library | What it does | Analogy |
|---|---|---|
| **pandas** | Loads and manipulates tables of data (called "DataFrames") | Excel, but in code |
| **numpy** | Fast maths on arrays of numbers. pandas is built on it | The calculator underneath |
| **matplotlib** | Draws charts | The pen |
| **seaborn** | Makes nicer statistical charts with less code (built on matplotlib) | A chart template |
| **scikit-learn** (imported as `sklearn`) | Builds, trains and evaluates machine learning models | The model toolbox |

## Jupyter notebook vs a script
- A **script** (`.py` file) runs from top to bottom in one go. It's good for finished, repeatable code.
- A **Jupyter notebook** (`.ipynb` file, short for "IPython Notebook") is split into **cells**. You run one cell at a time and see the output (tables, charts) right under it. You can also mix in text cells written in Markdown to explain what you're doing.

Notebooks are the standard tool for exploration and analysis, because you're constantly looking at the data, trying something, and looking again.

**Notebook gotcha:** cells can be run in any order, so a notebook can end up working only because you ran things in a strange order. Before you submit, always use **Restart Kernel and Run All** to make sure it runs "end to end", which the instructions ask for.

The **kernel** is the Python process running behind the notebook. It remembers every variable you've created. Restarting it wipes that memory.

## VS Code vs "Jupyter Notebook" the app
You don't need the separate Jupyter app. VS Code opens `.ipynb` files natively once the **Jupyter extension** is installed. You get the same notebook, inside the editor you already use.

## Virtual environments (venv)
A **virtual environment** is a private folder of Python packages that belongs to one project. Without one, every project shares the same packages, so upgrading something for project A can break project B.

```
python -m venv .venv            # create an environment in a folder called .venv
.venv\Scripts\Activate.ps1      # turn it on (PowerShell, Windows)
pip install pandas ...          # packages now install into .venv only
```

`pip` is Python's package installer. A `requirements.txt` file lists the packages a project needs, so anyone can recreate the environment with `pip install -r requirements.txt`. This is part of making your code "run end to end" on someone else's machine.

## Why it matters for this project
The submission must be a notebook or script that runs end to end. A venv plus a requirements.txt plus "Restart & Run All" is how you make sure it does.
