<a name="___top"></a>

- [cloc](#cloc)
- [Quick Start](#Quick_Start)
- [Basic Use ](#Basic_Use)
  - [Exclude Multiple Directories](#Exclude_Multiple_Directories)
  - [Exclude File Extensions](#Exclude_File_Extensions)
  - [Exclude List File](#Exclude_List_File)
  - [Exclude List File and Extensions](#Exclude_List_File_and_Extensions)


# cloc

*Count Lines of Code - (cloc)* counts blank lines, comment lines, and physical lines of source code in many programming languages. cloc is now being developed at https://github.com/AlDanial/cloc





<a name="Quick_Start"></a>

# [Quick Start &#9650;](#___top "click to go to top of document")



Install on Mac:

```bash
brew install cloc
```





<a name="Basic_Use"></a>

# [Basic Use &#9650;](#___top "click to go to top of document")



cloc is a command line program that takes file, directory, and/or archive names as inputs. Here's some examples of running cloc:



<a name="Exclude_Multiple_Directories"></a>

#### Exclude Multiple Directories

Exclude the given comma separated directories D1, D2, D3, et cetera, from being scanned. For example  --exclude-dir=.vendor,upload  will skip all files and subdirectories that have /vendor/ or /upload/ as their parent directory.

```bash
cloc --exclude-dir=vendor,upload .
```



<a name="Exclude_File_Extensions"></a>

#### Exclude File Extensions

Do not count files having the given file name extensions.

```bash
cloc --exclude-ext=css,html .
```



<a name="Exclude_List_File"></a>

#### Exclude List File

Ignore files and/or directories whose names appear in <file>.  <file> should have one file name per line.  Only exact matches are ignored; relative path names will be resolved starting from the directory where cloc is invoked. See also --list-file.

```bash
cloc --exclude-list-file=.clocignore .
```



We name ignore file name `.clocignore`

```text
vendor
logs
public/uploads
public/assets/css
public/assets/img
public/assets/fontawesome
public/assets/plugins/jquery
README.md
```



<a name="Exclude_List_File_and_Extensions"></a>

#### Exclude List File and Extensions

Ignore count files and/or directories whose names appear in `.clocignore`  and file extensions.

```bash
cloc --exclude-ext=md,css,html --exclude-list-file=.clocignore .
```



`.clocignore` file

```bash
vendor
logs
public/uploads
public/assets/css
public/assets/img
public/assets/fontawesome
public/assets/plugins/jquery
README.md
```

