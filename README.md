# Subatomic---Sherlock---HTB


# Task 1

- The `imphash` (Import Hash) is a technique used in malware analysis to identify similarities between different executable files based on their import tables. The import table contains the list of functions that an executable imports from DLLs (Dynamic Link Libraries). The `imphash` is a hash of this import table, and it can be used to detect files that may share similar functionality or are part of the same malware family.
- In order to get the `imphash`, I uploaded the file to `virustotal` and found it as illustrated in the following image.

![Screenshot 2025-02-17 122443](https://github.com/user-attachments/assets/0316a0cf-9cfe-44da-acde-dbeb8edd3aa7)

--------------------------------------------------------------------------
# Task 2 

- `SpcSpOpusInfo` 
	- It is a **metadata structure** inside a **Microsoft Authenticode digital signature**. It provides **optional** details about the signed executable, such as:
		✔ **Program Name** → The intended name of the software.  
		✔ **Publisher Information** → A brief description or URL related to the software.
	- I found the programs name in `virustotal` too.

   ![Screenshot 2025-02-17 122832](https://github.com/user-attachments/assets/5c3e6a44-4673-40d7-a7d0-27499aa4468a)
--------------------------------------------------------------------------
# Task 3

- A **GUID (Globally Unique Identifier)** is a 128-bit unique identifier used in software development to uniquely identify resources, components, or entities. It is typically represented as a 32-character hexadecimal string, formatted like this `{123e4567-e89b-12d3-a456-426614174000}`
	- I found the GUID of the program in a registry location as illustrated in the following screenshot.

   ![Screenshot 2025-02-19 133540](https://github.com/user-attachments/assets/c788601a-7df5-435d-ada9-1a17ec3f6972)

- In order to go to the folder that the malware is running from, I opened the task manager and got the location of the malware.

  ![Screenshot 2025-02-19 133959](https://github.com/user-attachments/assets/755482b3-57bd-4408-850b-93ea212f4196)

- **Locations The Malware Stores Info**
	- `C:\ProgramData\chocolatey`
	- `C:\ProgramData\ChocolateyHttpCache`
	- `C:\Users\[USER]\AppData\Local\serenitytherapyinstaller-update`
	- `C:\Users\[USER]\AppData\Roaming\SerenityTherapyInstaller\Local State` stores an encrypted key but I don't know the encryption type.
--------------------------------------------------------------------------
# Task 4

**What is the file extension `asar`?**
A **.asar** file is an **Electron Archive** format used by [Electron](https://www.electronjs.org/) applications to package resources such as JavaScript, HTML, CSS, and other assets. It is similar to a `.tar` archive but optimized for fast reads without needing to extract files.

I opened the folder of the malware in `C:\Users\Come-here-darling\AppData\Local\Programs\SerenityTherapyInstaller\resources` and I found `app.asar` file. 

![Screenshot 2025-03-03 134735](https://github.com/user-attachments/assets/16d3f49f-603f-432d-8ca3-0c9225640d28)


So I took it into a Linux machine to deal with it. I extracted the file using the following command `asar extract app.asar extracted-files` and opened the `extracted-files` folder to se `package.json` file in the folder. After opening the `package.json` file, I found the license of the software which is `ISC`. 

![Screenshot 2025-03-03 142859](https://github.com/user-attachments/assets/5484b071-e2ca-4f39-87f6-0c78d2810cb6)

--------------------------------------------------------------------------
# Task 5

In order to get the C2 address that the malware tries to reach, I executed the malware and captured the packets using Wireshark and the domain that is used for C2 is `illitmagnetic.site`.

![Screenshot 2025-03-03 154120](https://github.com/user-attachments/assets/6a1a8916-1293-49e6-a8ee-20e1f839e543)


--------------------------------------------------------------------------
# Task 6

After installing the missing packages like `dpapi` and `sqlite3`, I was able to run the `app.js` file. While using `vscode` to run and debug `app.js`, I saw a code in the `Loaded Scripts` section which is called `VM46947589` which contains code.

![Screenshot 2025-04-15 222844](https://github.com/user-attachments/assets/944b6d1b-25ce-4ab4-9524-3f1bb32f5a38)


I analyzed this code and found a URL to retrieve an IP address which is `https://ipinfo.io/json`.

![Screenshot 2025-04-15 223350](https://github.com/user-attachments/assets/4430ffab-36d6-4a3d-b82a-321136a8e688)

--------------------------------------------------------------------------
# Task 7

The malware tries to connect to a specific path in the C2 server which is `/api/` in the following URL `https://illitmagnetic.site/api/` and it exists in the same file `VM46947589`.

![Screenshot 2025-04-15 223830](https://github.com/user-attachments/assets/883cba51-260f-407b-a4f6-143e6333f02d)


--------------------------------------------------------------------------
# Task 8 

In the same `VM46947589` file, the variable that contains the user ID value is `duvet_user`.

![Screenshot 2025-04-15 224418](https://github.com/user-attachments/assets/6a9c4f26-394b-4fbe-9bb0-189ad24310bf)

--------------------------------------------------------------------------
# Task 9 

The host name that begins with `arch` is `archibaldpc` which exists in `VM46947589` file.

![Screenshot 2025-04-15 225458](https://github.com/user-attachments/assets/2e90c60b-e298-40b5-958b-db8a9d8d6596)

--------------------------------------------------------------------------
# Task 10

The malware author has mistakenly made a check of a process twice and its name is `vmwaretray`.

![Screenshot 2025-04-15 225821](https://github.com/user-attachments/assets/2d3942ac-d26f-4d02-a89b-3615bbe4d66c)

--------------------------------------------------------------------------
# Task 11

From the following screenshot, the path that the malware will write `cmd.exe` if doesn't exist in the path `C:\Windows\system32\cmd.exe` is `%USERPROFILE%\Documents\cmd.exe`.

![Screenshot 2025-04-15 231501](https://github.com/user-attachments/assets/27299ce8-a648-420f-be47-d1daa2d5996a)

--------------------------------------------------------------------------
# Task 12

To get the command that locates Firefox cookies, we go to the function `getFirefoxCookies` in `VM46947589` file and the command is `where /r . cookies.sqlite`.

![Screenshot 2025-04-15 232455](https://github.com/user-attachments/assets/0f50d846-d3d1-4b44-a90f-81937db38a49)

--------------------------------------------------------------------------
# Task 13

The modified discord module and the file injected are done by `discordInjection` function. The module is `discord_desktop_core-1` and I knew the file from the command `where /r . index.js` as the malware searches for the `index.js` file and stored in `discord_index` variable to be sent as an argument for the function `writeFileSync`.

![Screenshot 2025-04-15 233300](https://github.com/user-attachments/assets/f0c2be60-32f5-41d8-b3fe-f8f65482b661)
