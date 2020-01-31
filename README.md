# Sphinx Pages
|         |                 |
|:--------|----------------:|
|Version: |          **1.0**|  
|Platform:|**Windows, Unix**|  

A template repository to create sphinx documentation for Github pages.

## Indtroduction

Github pges is not simply compatible with Sphinx. One thing are the _folder which Github Pages Jekyll implementation ignores. 
The other issue is the folder structure of Sphinx, where the HTML output ist put into the ```$(BUILDDIR)/html``` folder.

A solution it to create a branch [gh-pages](https://github.com/Tyxz/SphinxPages/tree/gh-pages) where the html output is copied to. 
Additionally, create an empty file ```.nojekyll``` inside the root, to let Github know not to use *Jekyll*.  
Github will automatically find the special tree name and set up the page with the generated documentation.

A nicity for you is, that your document branch is completely independed of you development branches.

## Structure
```
SphinxPages::master:.
├───docs <- SphinxPages::gh-pages
└───src
    └───docs
        ├───build
        │   ├───doctrees
        │   └───html
        │       ├───_sources
        │       └───_static
        └───source
            ├───_static
            └───_templates
```

## Installation
To use this way, you have two choices: 1. clone this template repository 2. create a custom repository.
### Clone
1. Clone this repository: ```git clone git@github.com:Tyxz/SphinxPages.git [name]```
2. Clone it again and give it the name: ```git clone git@github.com:Tyxz/SphinxPages.git docs```
3. Enter docs and checkout gh-branches: ```git checkout -b gh-pages remotes/origin/gh-pages```
### Custom
1. Create a new repository: ```mkdir [name] && cd [name] && git init```
2. Add a Readme: ```touch Readme.md && git add Readme.md && git commit -m "Intial commit"```
3. Create a Github repository and link it: ```git remot add origin [git@github.com]```
4. Push your commit to it: ```git push -u origin master```
5. Clone your repository inti itself: ```git clone [git@github.com] docs && cd docs```
6. Create the gh-pages branch: ```git branch gh-pages```
7. Automatically switch to gh-branches when entering the folder: ```git symbolic-ref HEAD refs/heads/gh-pages```
8. Clean the branch: ```rm .git/index && git clean -fdx```
9. Change the Makefile
#### Windows
```batch
[...]

set BUILDDIR=build
set DOCS=../../docs

if "%1" == "" goto help
if "%1" == "github" goto github 

[...]

:help
%SPHINXBUILD% -M help %SOURCEDIR% %BUILDDIR% %SPHINXOPTS% %O%
goto end

:github
rmdir /S /Q "%BUILDDIR%"
%SPHINXBUILD% -M html %SOURCEDIR% %BUILDDIR% %SPHINXOPTS% %O%
copy /Y "%BUILDDIR%/html" "%DOCS%"
echo. 2>"%DOCS%/.nojekyll"
goto end

:end
popd
```

#### UNIX

```make
DOCS 		  = ../../docs

[...]

github:
	@rm -rf "$(BUILDDIR)"
	@make html
	@mv "$(BUILDDIR)/html" "$(DOCS)"
	@touch "$(DOCS)/.nojekyll"
```

## Use

Now, with two correctly set-up repositories, you can simply call ```make github``` to create your documentation and copy the html folder into your docs folder.
Afterwards, you are able to push these two repositories independently. Of course, you can also create a hook to do that for you if you push to master.

### Hook
```bash
echo Update documentation
cd /src/docs && make github
cd ../../docs
while true; do
    read -p "Do you wish to push the updated documenation?" yn
    case $yn in
        [Yy]* ) 
            git add *;    
            echo Add a commit message to your updated documentation
            read message
            git commit -m "$message"
            git push
            break;;
        [Nn]* ) exit;;
        * ) echo "Please answer yes or no.";;
    esac
done
exit 0
```
A hook like this, placed into the ```.git/hooks/``` folder, will automatically push the new documentation.