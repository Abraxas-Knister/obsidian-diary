# Obsidian Calendar seeder

I want to get into the habit of writing a summary each week, month,
quartal and year. This seeder creates the missing week, month, quartal
and year files and adds missing links between those files.

Moreover it backups the diary content in a local git repository and
shows an incremental diff to the last backup in obsidian upon closing.

## Missing files and links
For any daily entry present, a file with the naming scheme `wGGGG-VV`
will be created, for the ISO week that contains this day (cf.
[iso week](https://en.wiki.org/wiki/ISO_week_date)). This
file will end with a section

```md
# Associated Diary Entries
%% WARNING, don not append to or touch what follows %%

---
[[day-1]]
[[day-2]]
...
[[day-7]]
```

that lists out the existing daily entries that are contained in this week.

For any thus existing week entry, a file for the month that the week is in
will be created. If a week has days in two months at the same time, the two
different months link to the same week. Similarly, months are linked to by
quarter files and quarters are linked to by years.

The naming scheme is (cf. `man date`)

- **days**: YYYY-mm-dd
- **weeks**: wGGGG-VV
- **months**: mYYYY-mm
- **quarters**: qYYYY-q
- **years**: YYYY

## Requirements

This is a bash script I wrote for my own amusement without caring for portabiliy.

- The script is run with bash in a linux context
- It uses fdfind, ripgrep, rsync, kakoune and [obsidian-cli](https://github.com/Yakitrak/obsidian-cli)
- hardcoded values:
  - diary path is `~/notes/diary/`
  - `~/notes-bak/diary` exists and is a git repository
  - commits in this repo have the message `LANG=de_DE.UTF8 date` (with some format)
  - index is stored in ~/.local/share/mutables/obsidian-diary-index
