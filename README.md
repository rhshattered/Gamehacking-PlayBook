# Gamehacking-PlayBook
**Simple Get Process Id**
```c++
//I will add more explanation to lines later
DWORD GetProcId(const char* procName)
	{
		DWORD procId = 0;
		HANDLE hSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
		if (hSnap != INVALID_HANDLE_VALUE)
		{
			PROCESSENTRY32 procEntry;
			procEntry.dwSize = sizeof(procEntry);

			if (Process32First(hSnap, &procEntry))
			{
				do
				{
					if (!_stricmp(procEntry.szExeFile, procName))
					{
						procId = procEntry.th32ProcessID;
						break;
					}
				} while (Process32Next(hSnap, &procEntry));

			}
		}
		CloseHandle(hSnap);
		return procId;
	}
```
Demonstration </br>
```c++
//Create a DWORD variable since GetProcId returns DWORD
//DWORD is used to store hexadecimal variables like 0x539
DWORD procId; 

//This will retrieve the process id of the exe you choose.
//This is very useful because this is one of the ways of identifying a "target" process for whatever you want to do with it
//OpenProcess requires a process id to actually work we will cover OpenProcess later
procId = GetProcId("notepad.exe");
```
**DLL injection**
A classic amongst game hacking or just generally malware </br>
This forcibly loads a dynamic link library into a target process allowing inside access to any memory allocation in the process</br>
There are a million ways to do dll injection some being sneakier than others </br>
I will add c++ code showing a way to do it later </br>

**Random Generation**
My god this quite literally exists in every single game ever and every "cheat" loader ever.</br>
Randomly generated maps and bullet spread are prime examples of usage cases </br>
As for cheat devs they use this to randomize file names or console/window titles for there loader </br>
There are 3 functions needed to do this on windows </br>
-Getting current file name/path </br>
```c++
	std::string Path()
	{
		char path[_MAX_PATH]; // defining the path
		GetModuleFileNameA(NULL, path, _MAX_PATH); // getting the path
		return std::string(path); //returning the path
	}
```
-Generating a new random name for that file</br>
```c++
	std::string RandString(size_t length)
	{
		auto randchar = []() -> char
		{
			const char charset[] =
				"0123456789"
				"ABCDEFGHIJKLMNOPQRSTUVWXYZ"
				"abcdefghijklmnopqrstuvwxyz";
			const size_t max_index = (sizeof(charset) - 1);
			return charset[rand() % max_index];
		};
		std::string str(length, 0);
		std::generate_n(str.begin(), length, randchar);
		return str;
	}
```
-And Finally putting it all together
```c++
	void RandomExe(size_t size) //Renames active file something random on startup to prevent signatures size_t is for the length of the file name
	{
		std::string name = RandString(size) + ".exe";//Generating a beautifully named exe 
		std::rename(Path().c_str(), name.c_str()); //And applying it
	}
```
**Thats all for now folks more to be added later because this was written on 8/1/2021 at 10:03pm**
