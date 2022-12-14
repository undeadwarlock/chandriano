# chandriano
MemScanner for strings search in Virtual Memory. Live Protection, Input PID and On-Demand Scan Modes.

------------------------------------------------------------
FEATURES IN DEVELOPMENT (ALL OUT UNTIL END OF SEPTEMBER
------------------------------------------------------------

° RULE CREATION 

° VERBOSE 

° TERMINATE MALWARE

° ON-DEMAND SCAN 

° LOGGING RESULTS 

° VIRTUALPROTECTEX

° GETTING PID VIA KERNELSPACE INSTEAD OF USERSPACE + MONITORING API HOOKING TO COUNTER BYPASS (these features will take much longer to be implemented)

-----------------------------------
[0] Archives.
-----------------------------------
chandriano.py -> the tool itself.

watcher.py     -> WMI process creation monitor to get all new pids in system. 

setup_apis.py  -> Exported to theBeholder.py. Ctypes background for win32apis and hex flags.

---------------------------------------------------------------------------------------------
[1] What is this tool ?
---------------------------------------------------------------------------------------------

theBeholder is a tool that scans processes virtual memory in search of Malware Strings.

----------------------------------------------------------------------------------------------
[2] How to use it.
----------------------------------------------------------------------------------------------
The tool has 4 parameters (and you use only 2 of them at the same time)

A-   -r/--realtime= 

Use -r to scan the Virtual Memory of every new process that spawns in usermode.

To use -r mode, you need to start watcher.py (CLI: python watcher.py) before.


B-   -p/--pid= 

Use -p to scan the Virtual Memory of a specific Process instead of every new process.


C-   -s/--string=

Use -s to scan the Virtual Memory for a specific string. 

The tool will automatically convert your string to suit both Endianess states.

If the string has backspaces, please input it inside quotation marks.
"string with space"


D-   -d/--database=

Use -d to scan the Virtual Memory for a sequence of strings.

The tool will automatically convert all strings to suit both Endianess states.

-d input value is a txt or non-filetype archive with all desired strings.

The syntax is simple: one string in each line

string1

string2

string3

If you want to scan a string that has backspaces (like a shell command) and, in memory, has each piece of the string in a different location, then you may want to use the RULES argument and create a rule with "and" conditionais between each substring. Rules Argument WIP.



--------------------------------------------------------------------------------
[3] Engine
--------------------------------------------------------------------------------

A- Enabling Debug Privilege so it's possible to Read Virtual Memory and retrieve MEMORY_BASIC_INFORMATION.

OpenProcessToken -> Getting the Token of CurrentProcess() so it's possible to adjust it.

LookupPrivilegeValue -> Getting the LUID of SE_DEBUG_NAME

AdjustTokenPrivileges -> Enabling SE_DEBUG_NAME for Current Process using its token.

B- Memory Scanner

BaseAddress = 0

Loop while BaseAddress < MaxAppAddress (max base address given for a process virtual memory)

OpenProcess -> Opening Process Handle

VirtualQueryEx (starting from Base Address 0) -> Retrieving MEMORY_BASIC_INFORMATION about process pages.

create_string_buffer(mbi.RegionSize) -> Creating a Buffer of Page's Size

ReadProcessMemory -> "Writing" the Page Memory in the created buffer.

Base Address (0) += mbi.RegionSize -> adding the page size to Base Address so the next VirtualQueryEx retrieves

information about the next page of the process.

Loop.

C- Main

If -p is used, memscanner scans the pid.

If -r is used, memscanner scans all pids generated by the watcher.py.

Watcher.py logs the pid of all new processes in a file.

It stores 10 pids in each line, which allows for a perfect synchronization between the memscanner and 

the watcher. It works so well that no need of socket/multiprocessing is necessary.

-----------------------------------------------------------------------------------------------
[4] Evasion 
-----------------------------------------------------------------------------------------------

The best way to evade this tool is by usermode hooks because the tool is fully usermode.

It's important to say that sleeping techniques or others that are used to evade Sandbox doesnt work because 

of how COMMIT virtual memory pages work.

----------------------------------------------------------------------------------------------
[5] Future Improvements
----------------------------------------------------------------------------------------------

Countering bypass is always a thing, but apart from that:

[1] Verbose       (easy)

[2] Rule Creation (easy)


