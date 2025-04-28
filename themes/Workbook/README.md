# Basic Hugo Theme for Workbooks

**warning: in 2025, we switched to using the "main" branch - the "master" branch is historical**

**warning: in 2025, we needed to switch away from hugo relative URL, so the static and for_students shortcodes are hacks**

Created by Mike Gleicher in January 2020

Features:

- very minimal (the pages are simple, minimum extra junk)
- MathJax support (need to set the `mathjax` page parameter )

Things to remember when setting up a site:

- the main page needs to be "\_index.md" not "index.md" (otherwise, the docs directory doesn't build)
- the "for_students" directory needs to go into static
- you need to have the appropriate stuff in the config file
  - set the markdown processor for "unsafe"
  - set the baseurl to none
  - set the style for code markup
  - disable taxonomies
  - see the example toml file below
  - use weights to get the ordering of the docs right
  - use relativeURLs! (relativeURLs = true) - **caution - this doesn't work right!**
- if you want MathJax, you need to configure the page
- some stuff has been moved to the "mixin" for the particular year (to keep the workbook theme generic)
- the pages database (data/pages.yaml) needs to be there (use a mixin theme)
- delete the archetypes in the site, so that the theme has priority
- set the workbook number to -1 if you this isn't in the workbook database

Some caveats / bugs to fix:

- Right now, the resizing is a bit weird (the navigation goes away before the main column shrinks)
- the need to make the main page be \_index not index is weird
- things are still a bit buggy
- it's unclear what happens if there are real files outside of docs
- hugo creates a bogus archetype page in the site, which overrides the theme

**warning: the names of the submodules have changed - be sure to put them in the right place**

In 2021, Wiley fixed the setup - rather than having each workbook have its own copy 
of everything, we make them refer to master copies (so there is one big repo for the
entire semester's worth of workbooks).

Much of what is below is not longer applicable. Copy from known working workbooks.


The process:

1. `hugo new site [site name]`
2. `cd [site name]`
3. `git init`
4. `cd ./themes/`
5. `git submodule add git@github.com:CS559/Workbook.git` (themes/Workbook)
6. `git submodule add git@github.com:CS559/sp20.git` (themes/sp20)
7. `cd ../static/`
8. `mkdir libs`
9. `cd libs`
10. `git submodule add git@github.com:CS559/CS559-Libs21.git` (static/libs/CS559/)
11. `git submodule add git@github.com:CS559/CS559-Framework.git` (static/libs/CS559-Framework/)
12. `git submodule add git@github.com:CS559/CS559-THREE.git` (static/libs/CS559-Three21/)
13. `cd ../..`
14. `git submodule init`
15. `git submodule update`
16. (Optional) If you plan on updating submodule code when it's a submodule of the [site name] repo instead of when it's its own repo, you will want also want to run:
    1. `git submodule foreach --recursive git checkout master`
    2. `git submodule foreach --recursive git pull`
17. Edit `config.toml` to make it seem correct. (Template Below)
18. Delete the default.md from the archetypes
19. `hugo new _index.md` file
20. `hugo new docs/1.md` file
21. Create the rest of the docs files.
22. Create the `static/for_students` directory
23. Add .gitignore file. (Template below)

A simple `config.toml` file:

```toml
baseURL = ""
languageCode = "en-us"
title = "My New Hugo Site"
theme = ["sp20","Workbook"]
disableKinds = ["taxonomy", "taxonomyTerm"]
relativeURLs = true

[markup]
  [markup.highlight]
    style = "monokailight"
    codeFences = true
    lineNos = true

[markup.goldmark.renderer]
unsafe= true
```

A simple `.gitignore` file:

```txt
resources/_gen/
public/
.vscode
**/.DS_Store
```

The instructions above as a powershell script.
The file takes 3 params

1. site name
2. site title
3. number of docs pages to create not including index

```ps1
$name = $args[0]
$title = $args[1]
$num_docs = $args[2]

$toml = 'baseURL = ""
languageCode = "en-us"
title = "' + $title + '"
theme = ["sp20","Workbook"]
disableKinds = ["taxonomy", "taxonomyTerm"]
relativeURLs = true

[markup]
  [markup.highlight]
    style = "monokailight"
    codeFences = true
    lineNos = true

[markup.goldmark.renderer]
unsafe= true'

$ignore = 'resources/_gen/
public/
.vscode
**/.DS_Store'

hugo new site $name
Set-Location $name
git init
Set-Location ./themes/
git submodule add git@github.com:CS559/Workbook.git
git submodule add git@github.com:CS559/sp20.git
Set-Location ../static/
mkdir libs
Set-Location libs
git submodule add git@github.com:CS559/CS559-Libs.git
git submodule add git@github.com:CS559/CS559-Framework.git
git submodule add git@github.com:CS559/CS559-Three21.git
Set-Location ../..
git submodule init
git submodule update
git submodule foreach --recursive git checkout master
git submodule foreach --recursive git pull
Remove-Item 'config.toml'
New-Item 'config.toml'
[System.IO.File]::WriteAllLines(("./config.toml" | Resolve-Path), $toml)
Remove-Item ./archetypes/default.md
hugo new _index.md
For ($i = 1; $i -le $num_docs; $i++) {
    $doc = 'docs/' + $i + '.md'
    hugo new $doc
}
mkdir ./static/for_students
New-Item '.gitignore'
[System.IO.File]::WriteAllLines(("./.gitignore" | Resolve-Path), $ignore)
Set-Location ..
```
