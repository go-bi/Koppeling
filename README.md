# Koppeling
This project is a demonstration of advanced DLL hijack techniques. It was released in conjunction with the "[Adaptive DLL Hijacking](https://silentbreaksecurity.com/adaptive-dll-hijacking/)" blog post. I recommend you start there to contextualize this code.

This project is comprised of the following elements:
- **Harness.exe:** The "victim" application which is vulnerable to hijacking (static/dynamic)
- **Functions.dll:** The "real" library which exposes valid functionality to the harness
- **Theif.dll:** The "evil" library which is attempting to gain execution
- **NetClone.exe:** A C# application which will clone exports from one DLL to another
- **PyClone.py:** A python 3 script which mimics NetClone functionality

The VS solution itself supports 4 build configurations which map to 4 different methods of proxying functionality. This should provide a nice scalable way of demonstrating more techniques in the future.
- **Stc-Forward:** Forwards export names during the build process using linker comments
- **Dyn-NetClone:** Clones the export table from *functions.dll* onto *theif.dll* post-build using NetClone
- **Dyn-PyClone:** Clones the export table from *functions.dll* onto *theif.dll* post-build using PyClone
- **Dyn-Rebuild:** Rebuilds the export table and patches linked import tables post-load to dynamically prepare for function proxying

The goal of each technique is to successfully capture code execution while proxying functionality to the legitimate DLL. Each technique is tested to ensure static and dynamic sink situations are handled. This is by far not every primitive or technique variation. The post above goes into more detail.

## Example

Prepare a hijack scenario with an obviously incorrect DLL
```
> copy C:\windows\system32\whoami.exe .\whoami.exe
        1 file(s) copied.

> copy C:\windows\system32\kernel32.dll .\wkscli.dll
        1 file(s) copied.

```

Executing in the current configuration should result in an error
```
> whoami.exe 

"Entry Point Not Found"
```

Convert kernel32 to proxy functionality for wkscli
```
> NetClone.exe --target C:\windows\system32\kernel32.dll --reference C:\windows\system32\wkscli.dll --output wkscli.dll
[+] Done.

> whoami.exe
COMPUTER\User
```
## Example1
```
NetClone\\NetClone.exe --target Bin\\brownie_x64.dll --reference C:\\Windows\\System32\\%1.dll -o Bin\\%1.dll
```
