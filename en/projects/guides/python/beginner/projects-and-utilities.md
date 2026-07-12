# Projects and Utilities

This page contains small beginner projects and common utility topics in simple English. Each section explains the idea and then shows code examples.

You will find
- Date and time basics: using `datetime`, parsing and formatting dates, computing day differences.
- Small project examples: Tic-Tac-Toe, simple command-line notepad, unit conversion tool.
- Project layout tips and practical advice.

---

## Date and time: datetime module

Use the standard `datetime` module for date and time operations. Main classes:
- `datetime.date` for dates
- `datetime.time` for time of day
- `datetime.datetime` for combined date and time
- `datetime.timedelta` for time differences

Examples
```python
from datetime import datetime, date, timedelta
now = datetime.now()
print(now)

today = date.today()
print(today)

print(now.strftime("%Y-%m-%d %H:%M"))

s = '2026-07-12 14:00'
dt = datetime.strptime(s, "%Y-%m-%d %H:%M")

tomorrow = today + timedelta(days=1)
print((tomorrow - today).days)
```

Note: for time zones use `pytz` or `zoneinfo`.

---

## Project 1 — Tic-Tac-Toe (simple CLI)

Basic skeleton (same as Turkish version)
```python
# simple Tic-Tac-Toe skeleton

def create_board():
    return [' ' for _ in range(9)]

# ... same functions as before ...
```

Ideas: add a simple computer player or undo feature.

---

## Project 2 — Simple CLI Notepad

Basic file I/O example (same as Turkish version)
```python
FILENAME = 'notes.txt'
# add_note, list_notes, delete_note functions as in the Turkish file
```

Ideas: store notes as JSON, add search by keyword.

---

## Project 3 — Unit conversion tool

Example functions
```python
def celsius_to_fahrenheit(c):
    return c * 9/5 + 32

def km_to_miles(km):
    return km * 0.621371
```

Use a dict to map unit codes to functions for a simple CLI.

---

## Project layout tips

- Put repeated logic into functions.
- Separate utilities into modules (e.g. `utils.py`).
- Add tests with `pytest` or `unittest`.
- Validate user input and write clear commit messages.

---

## Tips

- Start with a working version, then add features.
- Keep code small and testable.
