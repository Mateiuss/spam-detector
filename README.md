# Spam Detector (Tema 4 PC)

A small C project that classifies a set of emails as **spam / not spam** using simple heuristics:
- keyword occurrences in the email body
- “too many capital letters” in the email body
- known spammer addresses (with an associated score)

The program reads input data from the `data/` directory and writes two output files:
- `statistics.out` — keyword statistics (task 1)
- `prediction.out` — one `0/1` prediction per email (task 2)

## Project structure

- `spam_detector.c`  
  Program entry point. Loads keywords, loads emails, computes all scores, writes outputs, and frees memory.

- `functions.c`  
  Implementation of all helper logic: reading inputs, scanning emails, computing standard deviation, scores, spam decision, and output.

- `headers.h`  
  Data structures, constants (paths), and function declarations.

- `Makefile`  
  Build rules for the executable `spam_detector`.

## Input files / expected dataset layout

The code expects these paths (see `headers.h`):
- `data/emails/` — a directory containing the email files (regular files).  
  Each email is opened by building a path like: `data/emails/<index>` (e.g. `data/emails/0`, `data/emails/1`, ...).
- `data/keywords` — keywords file:
  - first line: number of keywords `N`
  - next `N` lines: one keyword per line
- `additional_keywords` — extra keywords file (extends the keyword list):
  - first line: number of additional keywords `M`
  - next `M` lines: one keyword per line
- `data/spammers` — known spammers file:
  - first line: number of spammers `K`
  - next `K` lines: `<email_address> <score>`

**Important:** keyword searching and capital letter checks only apply to the email content starting from the line containing `Body:` (inclusive).

## How it works

### 1) Keyword statistics (`statistics.out`)
For each *original* keyword from `data/keywords` (not the additional ones), the program computes:
- total number of occurrences across all emails (case-insensitive)
- standard deviation of occurrences per email

Output format per line:
```
<keyword> <total_count> <stdev>
```

### 2) Spam prediction (`prediction.out`)
For each email, it computes:
- `key_score`: based on how many keywords appear, normalized by email body size
- `has_caps`: `1` if uppercase letters are more than half of the letters in the body, else `0`
- `spam_score`: score if the sender matches a known spammer address, else `0`

Final score:
```
final_score = 7 * key_score + 30 * has_caps + spam_score
```

Classification:
- `is_spam = 1` if `final_score > 35`
- otherwise `is_spam = 0`

`prediction.out` contains one integer per line, in email index order.

## Build and run

### Build
```sh
make build
```

This produces the executable:
- `./spam_detector`

### Run
```sh
./spam_detector
```

### Clean
```sh
make clean
```

Removes:
- `spam_detector`
- `statistics.out`

## Notes
- The program also writes a debugging file `logs.txt` with internal details (keyword stats and per-email computed fields).
- It links with `-lm` (math library) for `sqrt` used in standard deviation.
