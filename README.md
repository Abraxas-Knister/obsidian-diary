# Obsidian Calendar seeder

I want to get into the habit of writing a summary each week, month,
quarter and year. This seeder creates the missing week, month, quarter
and year files and adds missing links between those files.

Moreover it backups the diary content in a local git repository and
shows an incremental diff to the last backup in obsidian upon closing.

## Invocation

Since the script `cd`s into the hardcoded diary directory and does not take any arguments, you can invoke it from anywhere.
Just add the script to the `PATH`. I recommend setting up a command in obsidian via the [shellcommands plugin](https://github.com/Taitava/obsidian-shellcommands).

## Missing files and links

A day is considered to be contained in its [ISO week](https://en.wikipedia.org/wiki/ISO_week_date)).
If a diary entry for that week is not present in the diary it is considered
missing and will be added by the script. Whether just created or already existing, it will
also be modified to contain links to the existing daily entries that are contained in that week.

A week is considered to be part of the month its days are in (or part of the two
months its days are in) and the file for this month (or those two months) is constructed to contain
links for the weeks that are both part of the month and are existing in the diary.

Similarly, files for quarters linking to month files and files for years linking to quarters are generated.

The naming scheme is (cf. `man date`)

- **days**: YYYY-mm-dd
- **weeks**: wGGGG-VV
- **months**: mYYYY-mm
- **quarters**: qYYYY-q
- **years**: YYYY

**Example: ** If you add eg a daily entry for Wed. Jan 01 2025, the following happens

| **name of added file** | **link added to that file** | **reason**                                                            |
| :--------------------: | :-------------------------: | :-------------------------------------------------------------------- |
| 2025-01-01             | -                           | the day we added                                                      |
| w2025-01               | `[[2025-01-01]]`            | Thu of the week 2025-01-01 is in 2025, thus this is week 01 of 2025   |
| m2024-12               | `[[w2025-01]]`              | ISO week w2025-01 starts in Dec 2024                                  |
| m2025-01               | `[[w2025-01]]`              | w2025-01, which contains 2025-01-01 is in Jan 2025                    |
| q2024-4                | `[[m2024-12]]`              | Quarter 4 of 2024 contains Oct, Nov, Dec                              |
| q2025-1                | `[[m2025-01]]`              | Quarter 1 of 2025 contains Jan, Feb, Mar                              |
| 2024                   | `[[q2024-4]]`               | obvs.                                                                 |
| 2025                   | `[[q2025-1]]`               |                                                                       |

The links are gathered at the end of the file in a section that looks like this:
```md
# Associated Diary Entries

---

[[file-1]]
[[file-2]]
...
```
nothing should be appended to the file after the `---`.

## Requirements

This is a bash script I wrote for my own amusement without caring for portability.

- The script is run with bash in a linux context
- It uses fdfind, ripgrep, rsync, kakoune and [obsidian-cli](https://github.com/Yakitrak/obsidian-cli) (although replacing those with find, grep and sed should be relatively straigtforward).
- hardcoded values:
  - diary path is `~/notes/diary/`
  - `~/notes-bak/diary` exists and is a git repository
  - commits in this repo have the message `LANG=de_DE.UTF8 date` (with some format)
  - index is stored in ~/.local/share/mutables/obsidian-diary-index

## Errors

None that I know, although it is unclear how well obsidian (especially wih sync enabled) takes simultaneous outside changes to many different files at once.
