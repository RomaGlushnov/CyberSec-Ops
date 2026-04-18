This method adds a command history to the standard Windows Command Prompt (CMD) that persists after a reboot, as well as convenient aliases.

Installation code CMD (enter as a single line):

```cmd
reg add "HKCU\Software\Microsoft\Command Processor" /v AutoRun /t REG_SZ /d "doskey quit=doskey /history ^>^> %USERPROFILE%\history.log ^& exit & doskey history=type %USERPROFILE%\history.log" /f
```
### Quick reference: 

```history``` — view the entire command history.

```quit``` — save the current commands to a file and exit the console.

```%USERPROFILE%\history.log``` — the path to the file where the commands are physically stored.

**Important:** To save the history, close the console using the quit command, not the “X” button.
