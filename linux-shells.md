# Course 7 - Module 3 — TryHackMe: Linux Shells

## Activity Overview
I completed the TryHackMe room **Linux Shells** to deepen my practical skills with common Unix shells and shell scripting. The room covered the differences between shells (bash, zsh, fish), basic scripting constructs, and hands-on labs where I wrote and adjusted scripts to perform tasks like authenticating a user and locating flags in log files.

## What I Did
- Reviewed and compared different shells: **bash**, **zsh**, and **fish** — their design goals and common use cases.  
- Practiced writing shell scripts that used **variables**, **loops**, and **conditional statements**.  
- Created a user authentication script (simulated for lab purposes) that checked credentials and handled success/failure cases.  
- Modified a provided script to parse log files and locate a flag, using text-processing utilities and pattern matching.

## Key Concepts (elaborated)
### Overview of different Linux shells (bash, fish, zsh)
- **bash (Bourne Again Shell)**: The most widely-used shell on Linux systems and the default on many distributions. It's POSIX-compatible and very script-friendly, making it ideal for automation and tooling. I use it when I need maximum portability and when following examples from most Linux resources.  
- **zsh (Z Shell)**: An interactive, feature-rich shell with powerful tab completion and configuration (oh-my-zsh). Great for interactive use and power users who want a more productive prompt. I noted zsh's additional scripting features and interactive niceties but stuck with POSIX-compatible constructs for portability.  
- **fish (Friendly Interactive Shell)**: Focuses on user-friendliness and sane defaults (autosuggestions, better syntax highlighting). Fish scripts are not always POSIX-compatible, so they’re best for interactive use rather than cross-platform automation. In labs, I tried a couple fish commands to compare ergonomics.

### Scripting with variables, loops, and conditional statements
- **Variables**: I practiced declaring variables and accessing them (`name="jelly"` then `echo "$name"`). I also handled positional parameters (`$1`, `$2`) for scripts that accept arguments. Remember to quote variables to avoid word-splitting and globbing issues.  
- **Conditionals**: Used `if`, `elif`, and `else` to check exit statuses and string/number comparisons. Example patterns I used:
  ```bash
  if [ "$user" = "admin" ]; then
    echo "Welcome, $user"
  else
    echo "Access denied"
  fi
  ```
  I also used `[[ ... ]]` in bash/zsh for more flexible pattern matching (e.g., `[[ "$pw" == "secret"* ]]`).  
- **Loops**: Practiced `for` and `while` loops to iterate over files and lines. Example:
  ```bash
  for file in /var/log/*.log; do
    echo "Processing $file"
  done
  ```
  And `while read -r line; do ... done < file` for robust line-by-line processing.

### Created a script that authenticates a user
- I wrote a simple authentication script that accepts a username and password (or token) and checks them against a stored value (a simulated file for the lab). The script validated inputs, avoided storing plaintext secrets in the script itself, and returned distinct exit codes for success vs failure. Key lessons:
  - Validate input presence (`[ -z "$1" ] && exit 2`).  
  - Use `read -s` for hidden password input during interactive prompts.  
  - Consider exit codes: `0` for success, non-zero for various error states (1 = general failure, 2 = missing args).

### Adjusted a script to find a flag in log files
- The lab required finding a flag hidden in logs. I adjusted the script to:
  - Iterate through relevant log files (e.g., `/var/log/` or lab-provided paths).  
  - Use `grep -E`/`egrep` or `awk`/`sed` to match common flag patterns (for example `THM{...}` or `flag{...}`). Example:
    ```bash
    grep -Eo "flag\{[^}]+\}" /path/to/logs/* || true
    ```
  - Handle binary or compressed logs by checking file types and using `strings` or decompressing where necessary.  
  - Add logging and safety checks to avoid false positives (e.g., ensure matches are not in backup files).

## Tools & Commands I Used
- `bash`, `zsh`, `fish` (interactive comparisons)  
- `grep`, `egrep`, `awk`, `sed`, `strings`  
- `read -s`, exit statuses, test operators `[ ... ]` and `[[ ... ]]`  
- File iteration patterns (`for file in /path/*; do ... done`)  
- Redirects and pipes to chain utilities

## Personal Takeaways
This room reinforced how powerful small shell scripts are for automating common security tasks: parsing logs, extracting indicators, and quickly validating assumptions. I focused on writing portable, readable scripts (favoring POSIX-style constructs when possible) because in incident response I’ll often run scripts on a variety of Linux systems. The authentication scripting exercise highlighted the security risks of handling credentials and reminded me to avoid storing secrets in cleartext and to prefer ephemeral tokens or hashed checks when appropriate. Modifying the log-parsing script to find a flag was a nice practical reminder of using text-processing tools together — a core daily skill for security analysts.

## Next Steps / Study Questions
- Practice writing a script that accepts command-line options (use `getopts`) to make the log-search more flexible.  
- Create a hardened version of the authentication script that uses a hashed password file (e.g., compare bcrypt/sha hashes) instead of plaintext.  
- Build a small toolkit of shell one-liners for quick triage (top offenders in logs, IP extraction, suspicious command detection).

---
*File generated for GitHub as a first-person lab note.*
